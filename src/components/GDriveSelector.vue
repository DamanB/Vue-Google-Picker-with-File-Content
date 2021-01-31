<template>
  <div id="GDriveSelector">
    <h2>Click to open your Google Drive</h2>
    <button @click="driveIconClicked()">Open Google Drive</button>
    <FileContainer :fileName="fileName" :fileContent="fileContent" v-if="showFileContainer"
    />
  </div>
</template>

<script>
import FileContainer from "./FileContainer";
export default {
  components: {
    FileContainer,
  },
  mounted() {
    //Include Googles API Script. Appends to documents <head>
    let gDrive = document.createElement("script");
    gDrive.setAttribute("type", "text/javascript");
    gDrive.setAttribute("src", "https://apis.google.com/js/api.js");
    document.head.appendChild(gDrive);
  },
  data() {
    return {
      pickerApiLoaded: false,
      developerKey: "YOUR API KEY HERE", //Google project API key
      clientId: "YOUR CLIENT ID HERE", //Google project OAuth Client ID
      scope: "https://www.googleapis.com/auth/drive.readonly", //scope is set for readonly
      oauthToken: null,
      showFileContainer: false,
      fileName: null,
      fileContent: null,
    };
  },
  methods: {
    //Called when user clicks on drive icon
    async driveIconClicked() {
      console.log("Clicked");
      await gapi.load("auth2", () => {
        console.log("Auth2 Loaded");
        gapi.auth2.authorize(
          {
            client_id: this.clientId,
            scope: this.scope,
            immediate: false,
          },
          this.handleAuthResult
        );
      });
      gapi.load("picker", () => {
        console.log("Picker Loaded");
        this.pickerApiLoaded = true;
        this.createPicker();
      });
    },

    //handles the result from the google Auth attempt. Creates picker if success
    handleAuthResult(authResult) {
      console.log("Handle Auth result", authResult);
      if (authResult && !authResult.error) {
        this.oauthToken = authResult.access_token;
        this.createPicker();
      }
    },

    //Creates the picker
    createPicker() {
      console.log("Create Picker", google.picker);
      if (this.pickerApiLoaded && this.oauthToken) {
        var picker = new google.picker.PickerBuilder()
          .enableFeature(google.picker.Feature.MULTISELECT_ENABLED)
          .addView(google.picker.ViewId.DOCS)
          .setOAuthToken(this.oauthToken)
          .setDeveloperKey(this.developerKey)
          .setCallback(this.pickerCallback)
          .build();
        picker.setVisible(true);
      }
    },

    //callback from the picker
    async pickerCallback(data) {
      console.log("PickerCallback", data);
      if (data[google.picker.Response.ACTION] === google.picker.Action.PICKED) {
        //get only first document of array of selected docs
        var doc = data[google.picker.Response.DOCUMENTS][0];
        if (doc) {
          this.fileName = doc.name
          //generate the download URL for this doc
          //the alt=media is important for ensuring the content of the file is placed in response body
          var downloadUrl =
            "https://www.googleapis.com/drive/v2/files/" +
            doc.id +
            "?key=" +
            this.developerKey +
            " HTTP/1.1&alt=media";
          this.downloadFile(downloadUrl, this.displayFileContent);
        }
      }
    },

    //performs GET for the content of the file
    async downloadFile(downloadUrl, callback) {
      if (downloadUrl && this.oauthToken) {
        var accessToken = this.oauthToken;
        var xhr = new XMLHttpRequest();
        xhr.open("GET", downloadUrl);
        xhr.setRequestHeader("Authorization", "Bearer " + accessToken);
        xhr.onload = function () {
          callback(xhr.responseText);
        };
        xhr.onerror = function () {
          callback(null);
        };
        xhr.send();
      } else {
        callback(null);
      }
    },

    displayFileContent(content) {
      if (content)
      {
        //content was retrieved from the GET Request
        this.fileContent = content;
        this.showFileContainer = true;
      }
      else
      {
        //download file attempt failed
        this.fileContent = null;
        this.showFileContainer = false;
      }
    },
  },
};
</script>

<style scoped>
#GDriveSelector {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>