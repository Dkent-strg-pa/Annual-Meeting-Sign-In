# Setup: Google Sheets Backend for Annual Meeting Sign-In

This app uses a Google Sheet as the database and Google Apps Script as a free serverless API.

## Step 1: Create the Google Sheet

1. Go to [Google Sheets](https://sheets.google.com) and create a new spreadsheet
2. Name it something like "Annual Meeting Sign-In"
3. In row 1, add these headers in columns A-D:
   - A1: `First Name`
   - B1: `Last Name`
   - C1: `Full Name`
   - D1: `Timestamp`
4. Note the spreadsheet ID from the URL: `https://docs.google.com/spreadsheets/d/SPREADSHEET_ID/edit`

## Step 2: Create the Apps Script

1. In the Google Sheet, go to **Extensions > Apps Script**
2. Delete any existing code in `Code.gs`
3. Paste the following code:

```javascript
// The spreadsheet ID — replace with yours, or leave blank to use the bound spreadsheet
var SHEET_NAME = 'Sheet1';

function doPost(e) {
  try {
    var data = JSON.parse(e.postData.contents);
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);

    var timestamp = Utilities.formatDate(
      new Date(),
      'America/Chicago',
      'MM/dd/yyyy hh:mm:ss a'
    );

    sheet.appendRow([
      data.firstName,
      data.lastName,
      data.fullName,
      timestamp
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: 'ok' }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);
    var rows = sheet.getDataRange().getValues();

    // Skip header row
    var submissions = [];
    for (var i = 1; i < rows.length; i++) {
      if (rows[i][0] === '' && rows[i][1] === '') continue; // skip blank rows
      submissions.push({
        firstName: rows[i][0],
        lastName: rows[i][1],
        fullName: rows[i][2],
        timestamp: rows[i][3]
      });
    }

    return ContentService
      .createTextOutput(JSON.stringify({ submissions: submissions }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ submissions: [], error: err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. Click **Save** (Ctrl+S)

## Step 3: Deploy as Web App

1. Click **Deploy > New deployment**
2. Click the gear icon next to "Select type" and choose **Web app**
3. Set:
   - **Description:** "Annual Meeting Sign-In API"
   - **Execute as:** "Me" (your Google account)
   - **Who has access:** "Anyone"
4. Click **Deploy**
5. Click **Authorize access** and go through the Google consent flow
   - If you see "Google hasn't verified this app", click **Advanced > Go to ... (unsafe)** — this is your own script
6. Copy the **Web app URL** (looks like `https://script.google.com/macros/s/.../exec`)

## Step 4: Paste the URL into index.html

Open `index.html` and replace the placeholder:

```javascript
const APPS_SCRIPT_URL = 'YOUR_APPS_SCRIPT_URL_HERE';
```

with your actual URL:

```javascript
const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```

Commit and push. GitHub Pages will deploy automatically.

## Step 5: Test

1. Open the GitHub Pages URL in your browser
2. Fill in a first and last name, click "Sign In"
3. You should see the green success message
4. Check the Google Sheet — a new row should appear
5. Click "View Attendance" — the entry should load
6. Test in multiple tabs simultaneously to verify concurrent submissions work

## Updating the Apps Script

If you modify the Apps Script code after the initial deployment:

1. Go to **Deploy > Manage deployments**
2. Click the pencil icon on your deployment
3. Under **Version**, select "New version"
4. Click **Deploy**

The URL stays the same, but Apps Script caches the previous version. You must create a new version for changes to take effect.

## How It Works

- **Sign-in (POST):** The form sends a `POST` to the Apps Script URL with `mode: 'no-cors'`. This avoids a CORS preflight request. The response is opaque (can't be read), so success is shown optimistically.
- **Attendance (GET):** A standard `fetch` GET request. Google's Apps Script endpoint handles the redirect chain and returns JSON with all submissions.
- **Concurrency:** Google Sheets natively handles concurrent `appendRow` calls — no race conditions.
- **No tokens exposed:** The Apps Script URL is not secret (it requires no authentication), but it can only append rows to your sheet. There are no API keys or tokens in the page source.
