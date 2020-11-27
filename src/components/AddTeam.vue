<template>
  <v-card>
    <v-card-title>
      <h2>Add team</h2>
    </v-card-title>
    <v-card-text>
      <v-form>
        <v-text-field
          label="name"
          prepend-icon="mdi-account-circle"
          required
          v-model="teamName"
        />
        <v-file-input
          v-model="caption"
          placeholder="Upload your documents"
          label="File input"
          multiple
          prepend-icon="mdi-paperclip"
          @change="previewImage"
        >
          <template v-slot:selection="{ text }">
            <v-chip small label color="primary">
              {{ text }}
            </v-chip>
          </template>
        </v-file-input>
      </v-form>
    </v-card-text>
    <v-divider></v-divider>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn @click="uploadFile" color="info">Add Team</v-btn>
    </v-card-actions>
    <p>{{ message }}</p>
  </v-card>
</template>

<script>
import FirebaseService from "../services/FirebaseService";
import firebase from 'firebase';

export default {
  name: "addteam",
  props: ["groupInfo"],
  data() {
    return {
      files: [],
      teamName: "",
      message: "",
      group: null,
      img1: "",
      imageData: null
    };
  },

  watch: {
    groupInfo: function(groupInfo) {
      console.log("new is", groupInfo);
      this.group = { ...groupInfo };
      this.message = "";
    }
  },

  methods: {
    previewImage(event) {
      this.uploadValue = 0;
      this.img1 = null;
      this.imageData = event.target.files[0];
      this.onUpload();
    },
    onUpload() {
      this.img1 = null;
      const storageRef = 
        .storage()
        .ref(`${this.imageData.name}`)
        .put(this.imageData);
      storageRef.on(
        `state_changed`,
        snapshot => {
          this.uploadValue =
            (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
        },
        error => {
          console.log(error.message);
        },
        () => {
          this.uploadValue = 100;
          storageRef.snapshot.ref.getDownloadURL().then(url => {
            this.img1 = url;
            console.log(this.img1);
          });
        }
      );
    },
    uploadFile() {
      const post = {
        photo: this.img1,
        caption: this.caption
      };
      FirebaseService.uploadFile
        .push(post)
        .then(response => {
          this.saveTeam(response);
        })
        .catch(err => {
          console.log(err);
        });
    },
    saveTeam(imgUrl) {
      const data = {
        name: this.teamName,
        image: imgUrl
      };
      var parsedobj = JSON.parse(JSON.stringify(this.group));

      FirebaseService.createNewTeam(parsedobj.key, data)
        .then(() => {
          this.message = "The tutorial was updated successfully!";
          this.teamName = "";
          this.$emit("refreshList");
        })
        .catch(e => {
          console.log(e);
        });
    }
  },
  mounted() {
    this.message = "";
    this.group = { ...this.groupInfo };
  }
};
</script>
