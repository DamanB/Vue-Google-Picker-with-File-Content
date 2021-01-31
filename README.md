# Vue-Google-Picker-with-File-Content
## Goal
The goal of this Vue application is to use the Google Picker API to select a file from the users Google Drive. Then the file is downloaded and its content is displayed on the web page. 

## Development
Previous web resources for Google Picker on Vue exist. The Google Picker used on this project was developed with the assitance of an article [found here](https://medium.com/timeless/google-picker-with-vue-2a39de7f36e) 

The main concern for this project was displaying the contents of a file once selected by the Google Picker. In order to do this, I utilized the Google Drive API documentation [found here](https://developers.google.com/drive/api/v2/reference/files/get)


## Getting Started
After cloning this project, several steps are still needed to setup the application. 

1. Ensure you have a project setup on the [Google API Console](https://console.developers.google.com) with an API key and a OAuth 2.0 Client ID. Follow the article in the Development tab for help on this. 

2. The following 2 APIs must be enabled on the project using this console
  - Google Picker API
  - Google Drive API
  
3. In the Vue component found here: '/src/GDriveSelector.vue' make the following changes to the data(),
  - set developerKey to your API Key
  - set clientID to your Client ID

4. In a terminal, run below to install dependencies
'''
npm install
'''
5) Now run below to run the project
'''
npm run serve
'''
  



