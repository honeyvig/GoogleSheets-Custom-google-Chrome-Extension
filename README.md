# GoogleSheets-Custom-google-Chrome-Extension
Creating a custom extension for Google Sheets or a plugin for Google Chrome that works within Google Sheets involves leveraging Google Apps Script and Chrome Extensions. Below, I'll guide you through building both:
Option 1: Google Sheets Add-on Using Google Apps Script

Google Apps Script allows you to create custom extensions for Google Sheets using JavaScript. Here's a simple outline on how you can create an add-on to interact with Google Sheets.
Step 1: Create a New Google Apps Script Project

    Open Google Sheets.
    Go to Extensions → Apps Script.
    In the Apps Script editor, delete any default code and add your custom code.

Step 2: Basic Example - Custom Function

Here's a simple example of a Google Sheets extension that allows users to calculate the sum of two cells using a custom function SUMCELLS.

Code (Google Apps Script)

// This function calculates the sum of two cells
function SUMCELLS(cell1, cell2) {
  return cell1 + cell2;
}

// Create a custom menu in Google Sheets when the spreadsheet is opened
function onOpen() {
  const ui = SpreadsheetApp.getUi();
  ui.createMenu('Custom Functions')
    .addItem('Sum Cells', 'sumCells')
    .addToUi();
}

// This function is triggered when the custom menu item is clicked
function sumCells() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const cell1 = sheet.getRange('A1').getValue();  // Cell A1 value
  const cell2 = sheet.getRange('A2').getValue();  // Cell A2 value
  const result = SUMCELLS(cell1, cell2);
  sheet.getRange('A3').setValue(result);  // Set the result in Cell A3
}

Step 3: Save and Authorize

    Save the script.
    Go to Run → onOpen to initialize the custom menu.
    Grant the necessary permissions for the script to interact with Google Sheets.

Step 4: Use the Extension

    Once the script is running, open your Google Sheet.
    You’ll see a new menu called Custom Functions in the toolbar.
    Click on Sum Cells to calculate the sum of cells A1 and A2, and display the result in cell A3.

Option 2: Google Chrome Extension to Interact with Google Sheets

If you want to create a custom Google Chrome extension that interacts with Google Sheets (using Google Sheets API), you will need the following components:
Step 1: Set Up Your Chrome Extension

    Create a directory for your extension, e.g., my-google-sheets-extension.
    Inside this folder, create the necessary files: manifest.json, background.js, popup.html, and content.js.

Step 2: Create the manifest.json

The manifest.json file will tell Chrome about your extension and its permissions.

{
  "manifest_version": 3,
  "name": "Google Sheets Integration",
  "description": "A Chrome extension to interact with Google Sheets",
  "version": "1.0",
  "permissions": [
    "identity",
    "https://www.googleapis.com/",
    "activeTab"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "action": {
    "default_popup": "popup.html"
  },
  "host_permissions": [
    "https://sheets.googleapis.com/*"
  ]
}

Step 3: Set Up the OAuth2 Authentication (background.js)

You need to authenticate users via OAuth2 to interact with Google Sheets API.

// background.js

chrome.runtime.onInstalled.addListener(() => {
  console.log("Google Sheets Extension Installed");
});

// OAuth2 Authentication flow
chrome.identity.getAuthToken({ interactive: true }, function(token) {
  if (chrome.runtime.lastError) {
    console.log("Error: " + chrome.runtime.lastError);
  } else {
    console.log("Authenticated with token: " + token);
    // Now you can use this token to make requests to the Google Sheets API
  }
});

Step 4: HTML Popup Interface (popup.html)

The popup will provide an interface to interact with the extension.

<!DOCTYPE html>
<html>
  <head>
    <title>Google Sheets Extension</title>
  </head>
  <body>
    <h1>Google Sheets Integration</h1>
    <button id="getSheetData">Get Google Sheet Data</button>
    <div id="sheetData"></div>
    <script src="popup.js"></script>
  </body>
</html>

Step 5: Handle Interaction (popup.js)

Here, we'll use the authenticated token to fetch data from a Google Sheet.

// popup.js

document.getElementById('getSheetData').addEventListener('click', function() {
  chrome.identity.getAuthToken({ interactive: true }, function(token) {
    if (chrome.runtime.lastError) {
      console.log("Error: " + chrome.runtime.lastError);
      return;
    }

    const sheetId = "your_google_sheet_id"; // replace with your actual Google Sheet ID
    const range = "Sheet1!A1:B10"; // Specify the range of data you want to fetch

    fetch(`https://sheets.googleapis.com/v4/spreadsheets/${sheetId}/values/${range}`, {
      method: "GET",
      headers: {
        Authorization: `Bearer ${token}`
      }
    })
    .then(response => response.json())
    .then(data => {
      console.log("Sheet Data: ", data);
      document.getElementById('sheetData').innerText = JSON.stringify(data.values, null, 2);
    })
    .catch(error => console.error("Error fetching data:", error));
  });
});

Step 6: Run Your Extension

    Open chrome://extensions/ in your Chrome browser.
    Enable "Developer mode".
    Click "Load unpacked" and select the directory where your extension's files are located.
    After loading the extension, you’ll see the extension icon. Click it to open the popup.

Conclusion

    Google Apps Script: This is the most straightforward way to extend Google Sheets with custom functions and automation.
    Chrome Extension: If you want your extension to interact with Google Sheets from a separate UI (like a popup), you can create a Chrome extension using OAuth2 authentication to access Google Sheets data and perform actions like reading, writing, or modifying the sheet.

You can customize the Google Sheets extension to suit your specific needs, whether you want to automate data input, fetch external data, or integrate with other Google services!
