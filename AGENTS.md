# Agent handover: manifests-automation

## Purpose and evidence

Help a nontechnical colleague operate and troubleshoot Lakshay's order automation tools. Read `README.md` first. Give concrete, short steps using the actual button labels. The separate Word handover document contains detailed business procedures; it is not included in this repository. Ask for the relevant section if the problem depends on it rather than inventing its contents.

This handover was checked on 9 November 2026 against the four uploaded HTML files and the relevant earlier chats: “Check order numbers on site”, “Create KiwiExpress order checker”, “Generalize supplier order processing”, “Build order lookup extension”, “Check LAS orders for Blaze” and “Build consignment lookup script”. Historical requirements below are carried forward so a new agent does not need those chats. Current files and new evidence must still be inspected when diagnosing a problem.

## Repository scope

| File | Runtime and purpose |
| --- | --- |
| `order-lookup-helper.html` | Style/Global Trading lookup, helper version `3`; read-only portal searches and CSV export |
| `Kiwi_Express_Lookup_Setup.html` | Kiwi Express checker v1; read-only tracking requests and CSV export |
| `LAS_Supplier_CSV_Automatic_v2_HD_Australia.html` | LAS confirmation batch, version `automatic-v2-20261019-hd-australia1`; automatic production confirmation/reference Save |
| `LAS_Supplier_Dispatch_Automatic_v7_Setup.html` | LAS dispatch batch v7; automatic dispatch Save for a selected supplier and carrier |

These are self-contained bookmark setup pages, not a conventional application source tree. No package manifest, build scripts, tests, extension ZIP, sample CSVs or Word guide were uploaded. Do not instruct the colleague to run nonexistent repository commands or install Node/Python just to use a tool. Do not assume the original developer's local folders exist on their device.

The setup pages retain links from the original development folder that do not resolve in this repository:

- Kiwi: `../outputs/Kiwi_Express_Order_Checker_Extension.zip`, `Sample_Input.csv`.
- Confirmation: `Orders_Import_Ready.csv`, `../CSV_Template.csv`, `../Victory_Huasheng_Template.csv`, `../LAS_Supplier_CSV_Checkpoint.html`.
- Dispatch: `batch/README.txt`, `Sample_Input.csv`, `LAS_Blaze_Dispatch_Manual_Setup.html`.

Their absence does not prevent the embedded bookmarks from running. Do not promise an extension install or manual-review version without obtaining the actual files. Do not substitute a similarly named tool from elsewhere without verifying its behavior.

## Shared operating model

- Download/extract the actual HTML and open it in desktop Chrome or Edge. A GitHub source view or another person's `file:///` link is not the setup file.
- Drag the tool's button to the bookmarks bar, then launch the bookmark on its target website. The embedded JavaScript runs in that site's existing browser session; it does not include credentials or another person's progress.
- LAS requires `https://local-admin.blindsonline.com.au` and an account permitted to edit/save orders. Style requires HTTPS on `cp.stylebyglobal.com`. Kiwi requires `https://my.kiwiexpress.co.nz`.
- LAS and Kiwi open helper windows using `window.open`/`window.opener`; allow site pop-ups and keep the original tab open. Style places its panel in the page and searches through a same-origin off-screen iframe.
- A new HTML download does not update an existing bookmark. Close earlier helpers, recreate/replace the bookmark from the new setup, and check the runtime version. For LAS, finish or cancel the user's own unsaved editor before starting another helper.
- Preserve full original order references, including AU/NZ prefixes, two-letter suffixes and suffixes such as `-1`. Preserve leading zeros in supplier/consignment references and keep them as strings.
- Use new daily input, not a hard-coded order list. Download reports before replacing a list. Progress is local to the browser/profile and relevant site/tab; it is not transferred with the setup HTML or shared through GitHub.
- The two LAS files here are automatic-saving versions. A repair request does not itself authorise a production batch. Diagnose with local fixtures; obtain explicit instructions for live changes. Keep customer emails suppressed in dispatch.
- Never remove Save receipts or clear browser storage as a routine fix. They prevent duplicate submission. Inspect the actual LAS order before handling any uncertain Save. A record on one device does not provide duplicate protection on another device.

## Global Trading / Style lookup

Entry: `order-lookup-helper.html`. Launch on the logged-in **Manage Order** search page, reached from `https://cp.stylebyglobal.com/dashboard`. Current runtime marks its panel `data-helper-version="3"`.

Workflow: paste list/report → **Load list / New run** → preview → **Start / Resume** → **Download CSV**. Loading any list deliberately clears the current run, even when references repeat from yesterday. Resume without reloading to retain checked rows. Duplicate input references collapse to the first position; a later supplied name can fill an earlier blank name.

The helper selects **Customer Reference**, enters the full reference and clicks the real **Search** button in a fresh same-origin iframe. Input supports plain references, Excel/CSV/Markdown tables and optional customer names. Accepted order pattern is `^AU\d+[A-Z]{2}(?:-\d+)?$` (case-insensitive).

Accept success only when the returned labelled **Customer Reference** matches the requested reference and **Order Number** is numeric. That numeric supplier order number becomes **Supplier Ref**. Accept a missing order only when the portal explicitly says no order was found for that exact reference. Login expiry, blocked frames, timeouts and unknown pages must stop with the order unchecked, not manufacture a `NOT FOUND` result.

CSV columns: `Order Ref, Supplier Ref, Customer Name, Despatch`. Names prefer labelled portal fields, then the supplied name, then blank. Read **Despatch** from the order-line table and retain the displayed wording. Blank/dash/N/A/absent values become `NOT FOUND`; distinct nonempty line values join with `; `. A missing Despatch value is independent of whether a valid supplier reference was found. Only checked rows are exported.

State: `sessionStorage['style-order-lookup-dynamic-v2']` in the portal tab. Do not promise recovery after closing that tab. Runtime functions worth locating: `parseOrders`, `controls`, `loadSearchPage`, `lookup`, `parseResult`, `readDespatch`, `save`.

## Kiwi Express checker

Entry: `Kiwi_Express_Lookup_Setup.html`. Runtime v1, popup `KIWI_EXPRESS_CHECKER_V1`. Launch from a Kiwi tracking page; requests use `https://my.kiwiexpress.co.nz/barcode/{order_number}` with the complete encoded input reference.

Input: one reference per line, a CSV, or a pasted Excel/Markdown table; use `Order Ref` and optional `Customer Name`. **Read list / reset results** previews a fresh list and resets results. **Start / resume** skips terminal `FOUND`/`NO_RESPONSE` rows and retries unfinished/review rows. **Stop** aborts the active request and retains completed results; reopening requires Start to continue.

Business distinction established in the original request:

- `FOUND`: a matching order heading and genuine tracking response. Any delivery status is acceptable; do not require “Delivered”.
- `NO_RESPONSE`: the recognised normal tracking layout has no order response. The empty layout's default “dispatched” message alone is not evidence of a match.
- `REVIEW`: mismatched returned reference, network/HTTP failure, login/challenge page or unrecognised layout. Pause rather than treating it as an empty tracking result.

Normal wait is user-controlled, 5–3600 seconds. HTTP 429 waits `Retry-After` plus 120 seconds, or an increasing fallback if the header is unusable, then retries. The saved cooldown survives reopening; Stop cancels active waiting but resuming honours the remaining deadline. Do not bypass website cooldowns to make the normal wait look faster.

State: `localStorage['kiwiExpressBookmarkV1']` through `KiwiBridge`; requests use the original tab's `fetch`. Bookmark and optional extension state are separate; the extension is not shipped here. Core objects/functions: `Kiwi`, `KiwiBridge`, `Kiwi.parse`, `classifyResponse`, `createRunner`.

CSV columns: `Order Ref, Customer Name, Result, Returned Order, Tracking No, Delivery Status, Receiver, Picked Up At, Delivered At, Details, Lookup URL, Checked At`. `Checked At` uses UTC ISO timestamps; pickup/delivery dates retain the website's wording/timezone. A Kiwi `FOUND` result is not a supplier-confirmation reference or automatically a dispatch-ready CSV.

## LAS supplier confirmation

Entry: `LAS_Supplier_CSV_Automatic_v2_HD_Australia.html`. Popup `LAS_GENERIC_CSV_BATCH`; globals `LASCSV`, `LASPortal`, `__lasGenericBatchVersion`. The filename reflects the latest addition, not an HD-only restriction.

Workflow: upload CSV → choose fallback supplier → optionally choose confirmation date → **Start automatic batch — SAVE**. For the chosen supplier only, select its verified items together, set **In Production - (Confirmed)**, fill the supplier reference/date, click Save once, reload the same order and verify persistence before advancing. Do not require a separate click/save per item.

Supported CSV families:

| Input headings | Interpretation |
| --- | --- |
| `Customer Reference Order Number, Supplier Ref Order Number, Customer Name, Supplier` | Standard original format |
| `Order Ref, Supplier Ref, Customer Name, Despatch` | Lookup export; choose fallback supplier |
| `LAS order, Supplier reference, Customer, Supplier, Despatch` | Alternate standard headings |
| `LAS Order, Supplier Ref, Customer, Supplier` | Hardback lookup export; that lookup tool is not shipped here |
| `Sales Order Number, Huasheng Order Number, Customer name` plus recognised supplier-sheet columns | Map order/reference/customer and infer **Victory Blinds (Huasheng)** |

Preserve parsing of BOMs, quoted commas, multiline headings, leading zeros and empty trailing columns. Ignore trailing unnamed columns only when their row values are also empty. Do not silently accept unexpected nonempty columns or conflicting duplicate references.

User-established rules:

- An explicit CSV supplier takes priority, followed by inferred Victory/Huasheng supplier, followed by the dropdown. Initial choices include Victory Blinds (Huasheng), Global Trading, Hard Back Blinds and HD Australia; exact additional LAS names can be entered/discovered.
- Map `Hardback`, `Hard Back`, `Hardback Blinds` and `Hard Back Blinds` to the exact Hardback supplier present on that order. Other suppliers must not be matched by loose substring guessing.
- Blank confirmation date resolves to today's **Australia/Sydney** date once for the whole run. A chosen date overrides it. `Despatch`, factory, ordering and shipment dates do not alter the confirmation date or LAS scheduled dispatch date.
- If **any** item of the selected supplier already has a supplier reference, skip the whole order/supplier row before editing (`SKIPPED_EXISTING_REFERENCE`). Do not overwrite existing references or fill only that supplier's remaining blank references.
- Skip `NOT FOUND`/`NOT_FOUND`/`NOT-FOUND` in either the order or supplier-reference field, and unresolved markers `AMBIGUOUS`, `REVIEW`, `PENDING`, `SEARCHING`, `STOPPED`. `NOT FOUND` in the informational Despatch field does not skip a valid order/reference.
- Verify displayed order identity, supplier, item count and other suppliers' unchanged values. Ignore nested specification tables, totals and layout rows when reading the product grid.
- Missing supplier/details, ambiguous controls and conflicting duplicates pause for a user choice. **Continue** must recheck current DOM/values rather than act on stale controls. Do not weaken checks just to make the queue advance.

Queue/results live in the helper's memory. Save-attempt receipts are in the LAS target tab's `sessionStorage`, keyed as `codex_las_generic_batch_save_v1:` plus `JSON.stringify([order, normalisedSupplier])`. Keep the same LAS tab for recovery; do not promise the full queue survives closing the helper.

Write an attempted receipt before Save and never automatically submit that attempt again. Verify returned values and a fresh page load. If verification fails, pause as `SAVE_UNVERIFIED`; **Continue** rechecks the outcome. Skip retains the receipt and records `SKIPPED_SAVE_UNVERIFIED`. Pause/Stop/Skip cannot undo a Save already submitted, and Skip must not click Save, Cancel or an unlock control. The user handles their own unsaved editor.

## LAS supplier dispatch

Entry: `LAS_Supplier_Dispatch_Automatic_v7_Setup.html`. Required banner **AUTOMATIC DISPATCH V7**, popup `LAS_BLAZE_DISPATCH_AUTOMATIC`. Historical Blaze names/storage prefixes are intentional compatibility details. Globals: `LASDispatchData`, `LASDispatchBridge`, `LASDispatchPortal`, `__LASDispatchAutoVersion`.

This evolved from a no-save review tool to automatic saving at the user's request, then gained carrier and supplier selection. Preserve the existing popup/opener/inline action pattern and reuse the original LAS tab or its order iframe. Handle full reloads and ASP.NET partial/status refreshes; reacquire DOM controls after refresh. A separate tab or extension-only API is not required.

Input: `Order Ref, Connote, ConnoteDate`. Recognised aliases include `Consignment Number` and `Despatch Date`. Map three separate columns. Dates accept day-first `DD/MM/YYYY` or ISO `YYYY-MM-DD`; the parser also accepts day-first hyphens. Preserve consignment zeros. Invalid/missing values and unresolved Connote markers are blocked. Identical duplicates share one Save per supplier; conflicting Connote/date duplicates require a corrected CSV. Retain every original CSV row in the report.

Supplier dropdown: **Blaze Blinds** (default), **Global Trading**, **HD Australia**, **Project Outlook**, **Victory Blinds (Huasheng)**. Match complete names, ignoring case/whitespace. Supplier is chosen for the batch, not inferred from another column. Progress/receipts are separate per order and supplier. Switching supplier alone does not start work; it restores that supplier's results for the same dataset.

Carrier defaults to **Direct Freight Express**, with 14 selectable entries: Australia Post, Direct Freight Express, DPD-Euro, Express Freight, Hunter Express, two Kiwi Express Couriers occurrences, NE DistriService, PBT, Primeline Express, SKU Logistics, TNT, Toll and XDP. The two Kiwi choices are explicitly labelled first/second LAS option: preserve occurrence/count matching and read actual option values from LAS. Supplier and Carrier are fixed while running; **Pause after current order** before changing them.

Eligibility and fields:

- Ignore rows without **Item Status**, rows without a supplier name, samples and nonproduct/layout rows. Earlier related supplier-grid readers falsely flagged numeric adjustment codes such as ADJ-BTNEW/ADJ-MKT and unnamed samples as unreadable suppliers. Do not treat such nonproduct rows as eligible supplier products.
- Select only named, non-sample products of the chosen supplier with **blank Consignment Number**. Preserve existing consignments even if they differ from the CSV.
- The user explicitly chose to update only blank-consignment products in a mixed order, not skip the whole order. Existing-consignment products stay unticked. If none remain eligible, skip without opening an editor.
- Open one eligible item-status link, verify the editor supplier, set **Dispatched**, select the chosen carrier, check **Do Not Send Dispatch Email To The Customer**, fill Connote and Date Dispatched, and select exactly the eligible items.
- Recheck supplier, carrier, no-email checkbox, date, reference and item identities immediately before Save. Ambiguous identities become REVIEW, not guessed selections. Keep supplier references, confirmation dates and all excluded/other-supplier rows unchanged.

Save/recovery requirements:

- Record durable Save intent before the single native **Save Changes** click. Verify the editor closed, targeted status/Connote/date match, protected/excluded values remain intact, then reload and verify again before `SAVED_VERIFIED` and advancing.
- Carrier and no-email are verified in the editor before Save. The saved product grid does not independently prove carrier persistence or that no email was sent; do not claim email-delivery logs were checked.
- Routine pre-Save errors become `REVIEW` and may continue after cancelling only an editor demonstrably opened/owned by this helper. An uncertain Save pauses and must never be resubmitted automatically.
- **Retry REVIEW / verify earlier Save** rechecks receipts. **Leave unverified Save for review** allows remaining rows to continue after the editor is closed, retaining the earlier receipt. **Pause after current order** finishes/verifies the current order before stopping.
- Receipts survive reopening, list clearing and CSV replacement. Changing the carrier must not bypass an earlier receipt. Storage failure must prevent an untracked Save. Do not reset state just because older results are still displayed.

Storage on the LAS origin: `localStorage` prefix `lasBlazeDispatchAutoV3:` for metadata/runtime/per-row outcomes; receipts `lasBlazeDispatchSaveReceiptV3:{order}` for Blaze, adding `:supplier:{encodeURIComponent(exactSupplier)}` for other suppliers. The target tab also has session ownership state. Keep historical keys and default-Blaze compatibility during upgrades. Per-row persistence and 100-row table pages were introduced for lists around 1,600 orders; avoid rewriting the whole dataset after each row.

Reports retain original rows plus `LAS dispatch:` result/details, Save-intent/verified UTC times, Carrier and Supplier. Outcomes include `SAVED_VERIFIED`, `ALREADY_DISPATCHED`, `SKIPPED_EXISTING_CONSIGNMENT`, `SKIPPED_NOT_SUPPLIER`, `SKIPPED_NO_ITEM_STATUS`, `SKIPPED_NOT_FOUND`, `REVIEW`, `INVALID_INPUT`, `SAVE_UNVERIFIED`, `PENDING`. Download each supplier's report separately.

## Related tools from earlier conversations, not included here

The Hardback lookup exported `LAS Order, Supplier Ref, Customer, Supplier`; Supplier was `Hardback`, and Supplier Ref was the full blue portal Ref No. Unresolved results must remain excluded from confirmation saves. Obtain that tool separately if needed.

The Direct Freight lookup provided `Connote` and `ConnoteDate`; **Despatch Date**, not delivery/ETA, is the dispatch date. Its later lookup used the full order reference in `https://www.directfreight.com.au/ConsignmentStatus.aspx?consignment_no={order}&lookuptype=1`, always `lookuptype=1`. Later versions added an actual Track-button/manual POST mode, a user wait setting and HTTP 429 cooldown of Retry-After + 120 seconds. A saved cooldown can remain while manual searches work. An old saved 20-second throttle overriding the normal wait was fixed in v4.1. These details explain historical CSVs/issues; this repo has no Direct Freight setup file. Do not apply its old version-specific retry behavior to Kiwi without inspecting Kiwi's code.

## Troubleshooting and repairs

1. Identify the exact setup filename, actual helper banner/version, browser, target page, input headings and full error text. Establish whether Save was attempted. Ask the colleague for only the missing evidence and one or two concrete steps at a time.
2. First check stale bookmarks, wrong page, expired login, blocked pop-ups, closed opener tabs, an existing unsaved LAS editor, incorrect column mapping and a saved REVIEW result. Do not require clearing progress to test these.
3. For a parser/control problem, obtain a small relevant page HTML/table fragment or screenshot and minimal anonymised CSV. Ask for the relevant Word-guide section if a business decision is unclear. Do not request passwords, cookies or tokens. Never commit live order lists, customer names, addresses or receipts as fixtures.
4. For uncertain Save, retain receipts and inspect the live outcome read-only before proposing any retry. Diagnose whether it is a pre-Save failure, postback wait, actual mismatch or failed reload verification. Do not translate an unfamiliar page into NOT_FOUND or success.
5. Preserve existing tool behavior and original files; the earlier development deliberately kept manual checkpoints separate. Make a targeted repair, keep lookup and saving workflows distinct, and do not create a replacement application without a reason.

### Inspecting the embedded code

Use an HTML parser to obtain the anchor whose `href` starts with `javascript:`, HTML-unescape through the parser and URL-decode once. The Style page also contains a plain-code textarea. The other three launchers contain `document.write(...)` for the helper UI and a JSON-quoted `script.textContent` runtime payload. Parse those string literals as data; do not execute a bookmark just to inspect it. LAS dispatch also embeds its portal engine inside an `install` function, so find every copy of a shared module before changing it.

Code is duplicated between the bookmark anchor and setup-page copy field/inline string. Style has its raw textarea too. When repairing a file, regenerate all relevant representations from the same code, preserve URL/HTML escaping, and update the runtime version/banner so the user can identify the fix. Do not globally replace text inside an encoded payload and assume the bookmark still works. Do not add remote script dependencies to these shareable setup files.

### Validation and delivery

There is no shipped test harness. For a code change, use isolated local browser fixtures with network interception for every relevant portal URL. Never let tests fall through to live LAS Save endpoints. Cover the actual reported failure and the material neighboring cases: matching/missing order, misleading/unknown response, input parsing/duplicates, supplier isolation, existing references/consignments, postback/reload, no-email, one Save and uncertain-Save recovery as applicable.

Check decoded JavaScript syntax and that the drag bookmark and manual-copy payload agree (Style's textarea uses the raw code rather than a full bookmark URL). Preserve Excel-safe CSV escaping, BOMs and string references. Verify unaffected HTML artifacts remain unchanged when only documentation or another tool was edited. Report what was tested and distinguish local simulation from signed-in live validation; historical passing checks are not proof of current website compatibility.

Hand back the updated setup file with simple instructions: finish/cancel the user's own edit, close the old helper, replace the bookmark, reopen it on the correct site, confirm the version, then use the appropriate resume/review control. Update the README if user-facing steps or shipped files changed.
