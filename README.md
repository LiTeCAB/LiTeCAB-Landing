# Landing Page (GitHub Pages)

This folder is deployed as a static landing page through GitHub Pages via:

- `.github/workflows/pages.yml`

## What is implemented

- Tool intro and value proposition
- Product walkthrough section
- Interest + inquiry form (includes a dedicated question field)

## Form storage (where submissions go)

Submissions are sent to the endpoint in `docs/script.js`:

```js
const SHEETS_ENDPOINT = "https://script.google.com/macros/s/REPLACE_WITH_DEPLOYMENT_ID/exec";
```

### Current behavior

- If the endpoint is not set, the form does **not** submit and shows a setup message.
- After you set a real Google Apps Script endpoint, submissions are appended as rows to your Google Sheet.

## Can we store submissions directly in Git?

Not safely from a static GitHub Pages form.

- GitHub Pages itself cannot write to files in the repo.
- Writing to GitHub via API would require a token, which should not be exposed in client-side JavaScript.

Recommended: submit to Google Apps Script, then store in Google Sheets.

## Setup steps

1. Create a Google Sheet named `LiTeCAB Leads`.
2. Add a header row (A1:J1):

   `submitted_at | name | email | organization | interest_level | next_step | use_case | inquiry | consent | source`

3. In that Sheet, open **Extensions -> Apps Script** and paste:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Sheet1');
  const p = e.parameter;

  sheet.appendRow([
    p.submitted_at || '',
    p.name || '',
    p.email || '',
    p.organization || '',
    p.interest_level || '',
    p.next_step || '',
    p.use_case || '',
    p.inquiry || '',
    p.consent || '',
    p.source || ''
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ ok: true }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

4. Click **Deploy -> New deployment**.
   - Type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Copy the Web app URL and replace `REPLACE_WITH_DEPLOYMENT_ID` in `docs/script.js`.
6. Push to `main` (or `master`) to trigger deployment.
7. In GitHub repo settings, ensure **Pages** source is set to **GitHub Actions**.

## Published URL

- Org/repo pages URL will be:
  - `https://<org>.github.io/<repo>/`
