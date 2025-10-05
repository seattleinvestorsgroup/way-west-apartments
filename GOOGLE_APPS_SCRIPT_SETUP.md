# Google Apps Script Setup for Tenant Issue Reporting

To connect the tenant issue reporting form to your Google Spreadsheet, you'll need to set up a Google Apps Script. Follow these steps:

## Step 1: Create a Google Apps Script Project

1. Go to [script.google.com](https://script.google.com)
2. Sign in with your Google account
3. Click on "New Project"
4. Delete any existing code in the Code.gs file
5. Replace it with the code provided below
6. Click "Save" and give your project a name like "Way West Apartments Issue Reporting"

## Step 2: Add the Script Code

Copy and paste the following code into your Apps Script project:

```javascript
// Replace this with your actual spreadsheet ID
const SPREADSHEET_ID = '1nqf_9NeeQkVzwsf3YWmo2_4A_vgQ_gWr4AqZvrDzzfQ';

// Replace this with the name of the sheet where you want to store the data
const SHEET_NAME = 'Sheet1';

function doPost(e) {
  try {
    // Get the data from the request
    const data = JSON.parse(e.postData.contents);
    
    // Open the spreadsheet and get the sheet
    const sheet = SpreadsheetApp.openById(SPREADSHEET_ID).getSheetByName(SHEET_NAME);
    
    // If sheet doesn't exist, create it
    if (!sheet) {
      sheet = SpreadsheetApp.openById(SPREADSHEET_ID).insertSheet(SHEET_NAME);
    }
    
    // If it's a new sheet, add headers
    if (sheet.getLastRow() === 0) {
      const headers = ['Tenant Name', 'Unit Number', 'Contact Number', 'Issue Priority', 'Issue Description', 'Date Submitted'];
      sheet.appendRow(headers);
    }
    
    // Add the data to the sheet
    const rowData = [
      data['Tenant Name'],
      data['Unit Number'],
      data['Contact Number'],
      data['Issue Priority'],
      data['Issue Description'],
      data['Date Submitted']
    ];
    
    sheet.appendRow(rowData);
    
    // Return success response
    return ContentService
      .createTextOutput(JSON.stringify({result: 'success'}))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (error) {
    // Return error response
    return ContentService
      .createTextOutput(JSON.stringify({result: 'error', message: error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet() {
  return ContentService
    .createTextOutput('Way West Apartments Issue Reporting Service')
    .setMimeType(ContentService.MimeType.TEXT);
}
```

## Step 3: Deploy the Script as a Web App

1. Click on the "Deploy" button in the Apps Script editor
2. Select "New deployment"
3. Click the gear icon and select "Web app"
4. Set the following options:
   - Description: Way West Apartments Issue Reporting
   - Execute as: Me (your email)
   - Who has access: Anyone (or Anyone with Google account, depending on your preference)
5. Click "Deploy"
6. Copy the Web app URL - you'll need this for the next step

## Step 4: Update the Form to Use Your Web App URL

1. Go back to your `report-issue.html` file
2. Find the line with the fetch request URL:
   ```javascript
   fetch('https://script.google.com/macros/s/AKfycbwc5BtUg6xLSulFSyVs5AHanhGmq5tByLGYno6YfVFMaZyt_F3a5sCjEvA9yMoqs_-vqg/exec', {
   ```
3. Replace the URL with your Web app URL from Step 3

## Step 5: Configure Spreadsheet Access

1. Open your Google Spreadsheet: https://docs.google.com/spreadsheets/d/1nqf_9NeeQkVzwsf3YWmo2_4A_vgQ_gWr4AqZvrDzzfQ/edit
2. Share the spreadsheet with your Apps Script project:
   - Click the "Share" button
   - Add the email address from your Apps Script project (you can find this in the Apps Script editor under Project Settings)
   - Give it "Editor" access

## Testing

Once everything is set up, you can test the form by:
1. Opening `report-issue.html` in your browser
2. Filling out the form with test data
3. Submitting the form
4. Checking your Google Spreadsheet to see if the data was added

## Troubleshooting

If you encounter issues:
1. Check that the spreadsheet ID in the script matches your actual spreadsheet
2. Verify that the Apps Script has the correct permissions to access the spreadsheet
3. Make sure the Web app is deployed with the correct access settings
4. Check the browser console for any error messages