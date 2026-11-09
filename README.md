# Order automation handover

These tools help look up orders and update LAS. This is a quick-start guide; use the separate Word document from Lakshay for the detailed work instructions.

You need Chrome or Edge, internet access, and your own authorised login for LAS or the supplier portal. You do not need to install any coding software.

## Choose the right tool

| What you want to do | File to open | Does it change orders? |
| --- | --- | --- |
| Find Global Trading supplier references and despatch information | [order-lookup-helper.html](order-lookup-helper.html) | No — lookup only |
| Check whether Kiwi Express has tracking information for your orders | [Kiwi_Express_Lookup_Setup.html](Kiwi_Express_Lookup_Setup.html) | No — lookup only |
| Add supplier references and confirm production in LAS | [LAS_Supplier_CSV_Automatic_v2_HD_Australia.html](LAS_Supplier_CSV_Automatic_v2_HD_Australia.html) | **Yes — saves automatically** |
| Mark a supplier's products as dispatched in LAS | [LAS_Supplier_Dispatch_Automatic_v7_Setup.html](LAS_Supplier_Dispatch_Automatic_v7_Setup.html) | **Yes — saves automatically** |

## Set up once on your computer

1. On this repository's main GitHub page, click **Code → Download ZIP**.
2. In **Downloads**, right-click the ZIP and choose **Extract All**.
3. Open the required `.html` file from the extracted folder in **Chrome or Edge**. Use right-click → **Open with** if needed.
4. Show the browser's bookmarks bar with **Ctrl + Shift + B**.
5. Drag the setup page's large tool button onto the bookmarks bar. This creates a bookmark that opens the tool.
6. Open the correct website below, sign in where required, then click your new bookmark. Allow pop-ups for that website if asked.

| Tool | Where to click the bookmark |
| --- | --- |
| Global Trading lookup | The **Manage Order** page in [Style Portal](https://cp.stylebyglobal.com/dashboard) |
| Kiwi Express checker | A [Kiwi Express tracking page](https://my.kiwiexpress.co.nz/barcode/EXAMPLE) — replace `EXAMPLE` with an order number |
| Either LAS tool | Your signed-in [LAS portal](https://local-admin.blindsonline.com.au) |

Click the saved bookmark while on the website, rather than on the setup page. If dragging fails, follow that page's manual bookmark instructions where available.

**The four HTML files contain everything needed for the bookmark method.** Some setup pages link to templates, an extension ZIP or older versions that are not included here. Use the CSV examples below or the Word guide instead.

## Global Trading: find supplier references

1. Launch the bookmark on **Manage Order** in Style Portal.
2. Paste order numbers one per line, or a report with **Order Ref** and **Customer Name** headings.
3. Click **Load list / New run**, check the preview, then **Start / Resume**.
4. Keep the tab open, then click **Download CSV** when finished.

The report contains **Order Ref, Supplier Ref, Customer Name, Despatch**. Missing despatch information shows `NOT FOUND`; this alone does not mean the order was missing.

To continue, use **Start / Resume**. Loading a list again clears the results; download them first.

## Kiwi Express: check tracking responses

1. Open a Kiwi Express tracking page and launch the bookmark.
2. Paste one order number per line, or upload a CSV with an **Order Ref** heading.
3. Click **Read list / reset results**, check the preview, choose a wait time, then **Start / resume**.
4. Keep both windows open. Use **Copy table** or **Download CSV** for results.

`FOUND` means tracking details were returned, regardless of delivery status. `NO_RESPONSE` means no matching details were returned. `REVIEW` needs checking.

Progress is saved in your browser. **Start / resume** continues; **Read list / reset results** starts over. Let any website cooldown countdown finish.

## LAS: add references and confirm production

**Start saves changes automatically.** Check the supplier, references and date, and finish or cancel any existing LAS edit first.

1. Launch the confirmation bookmark from LAS and upload a CSV.
2. Choose the supplier for rows that do not already name one. A supplier in the CSV takes priority over the dropdown.
3. Enter a confirmation date, or leave it blank to use today's date in Sydney for the whole run.
4. Click **Start automatic batch — SAVE**. Keep both windows open; let the tool navigate LAS.
5. Follow any selection prompts, then download the results CSV.

It sets **In Production - (Confirmed)** and adds the reference/date. For each order, it skips that supplier if any of its items already has a reference. Missing or unresolved order/supplier references are also skipped. Despite its filename, this tool supports multiple suppliers.

Upload the Global Trading lookup CSV directly and choose **Global Trading**, or make a CSV in Excel. Replace this fictitious example and save as **CSV UTF-8 (Comma delimited)**:

```csv
Order Ref,Supplier Ref,Customer Name,Supplier
AU1234567WB,1234567,Example Customer,Global Trading
```

An optional **Despatch** column does not change the confirmation or scheduled dispatch date.

## LAS: update dispatch details

**Start saves changes automatically.** Finish or cancel any existing LAS edit first.

1. Launch the dispatch bookmark from LAS. Its banner should say **AUTOMATIC DISPATCH V7**.
2. Upload **Order Ref, Connote, ConnoteDate**. “Connote” is the tracking reference to save in LAS; follow the handover guide for your carrier.
3. Check the columns, **Supplier** and **Carrier**. Defaults are Blaze Blinds and Direct Freight Express; change them as needed.
4. Click **Start / resume batch — SAVE**. Keep the LAS tab and helper open.
5. Download the results CSV. Use **Pause after current order** before changing supplier or carrier.

It sets **Dispatched**, fills tracking number/date, selects your carrier and ticks **Do Not Send Dispatch Email To The Customer**. Only the chosen supplier's products with blank consignment numbers are updated. Existing consignments, other suppliers, unnamed items and samples are left alone.

Suppliers: **Blaze Blinds, Global Trading, HD Australia, Project Outlook, Victory Blinds (Huasheng)**. Process and download results for each supplier separately.

Replace this fictitious example. Dates accept **DD/MM/YYYY** or **YYYY-MM-DD**:

```csv
Order Ref,Connote,ConnoteDate
AU1234567WB,001234567,09/11/2026
```

For Australian Direct Freight batches, use the consignment number and actual dispatch/despatch date from the carrier report. For the **New Zealand Bolts + Kiwi workflow**, include only matched orders with a valid Bolts Freight Connote and a Kiwi result of `FOUND`: set **Connote to the full Order Ref**, use the **Bolts Connote Date**, and choose **Kiwi Express Couriers (first LAS option)**. The detailed handover guide explains the matching steps.

## If something goes wrong

| What you see | What to do |
| --- | --- |
| No helper opens | Check that you clicked the bookmark on the correct website, are signed in where needed, and have allowed pop-ups. |
| An old version or the same old error | Close the old helper, open the latest setup file and **replace the bookmark**. Downloading a new file does not update a saved bookmark. |
| `REVIEW` or a request for a manual selection | Read the message and check that order. Do not guess missing references, suppliers or dates. |
| A Save could not be verified | Keep the LAS tab open and check the actual order. Confirmation: use **Continue** to recheck. Dispatch: use **Retry REVIEW / verify earlier Save**. These controls verify the earlier Save rather than submitting it again. |
| A Kiwi Express countdown | The website has asked the tool to wait. Keep it open; reopening it does not clear the saved wait. |

Download results before closing a helper or loading a new list. Sharing setup files does not share logins or progress. Keep the same LAS tab open during confirmation Save recovery; its Save record is tied to that tab.

For help from ChatGPT, attach **[AGENTS.md](AGENTS.md)** and the affected setup HTML file, then paste this message with the blanks filled in:

> Please read AGENTS.md and help me troubleshoot this tool. I am not technical, so give me one step at a time. Tool/file: ___. Browser: ___. What I clicked: ___. Exact message: ___. Expected result: ___. Did it click Save, or is that uncertain? ___. I have attached the setup file and a screenshot or small relevant CSV sample with unnecessary customer details removed.

Development checks used sample pages. Follow the Word guide to check results on the live websites.
