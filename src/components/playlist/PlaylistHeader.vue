<template>
  <v-container>
    <v-container>
      <v-container>
        <p class="body-2 font-weight-thin mb-0 pl-1 mt-sm-2 text-center">
          Playlist
        </p>
        <p
          class="font-weight-bold text-center"
          v-bind:class="[
            $vuetify.breakpoint.smAndUp ? 'display-3' : 'display-1'
          ]"
        >
          {{ playlist.name }}
        </p>
      </v-container>
      <v-container class="d-flex flex-wrap justify-space-around ">
        <v-btn
          v-on:click="editPlaylistDialog = true"
          color="indigo accent-4"
          class="mb-2 d-flex justify-center"
          >Edit playlist</v-btn
        >
        <v-btn v-on:click="deletePlaylistDialog = !deletePlaylistDialog"
          >Delete playlist</v-btn
        >
      </v-container>

      <v-dialog v-model="editPlaylistDialog" max-width="460">
        <v-card>
          <v-card-text class="pt-2 pb-0">
            <v-text-field
              v-model="newPlaylistName"
              label="Enter new playlist name"
            ></v-text-field>
          </v-card-text>
          <v-card-actions class="pt-0">
            <v-spacer />
            <v-btn v-on:click="changePlaylistName" color="green darken-2"
              >OK</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="invalidPlaylistName" max-width="500">
        <v-card>
          <v-card-title class="pb-0">
            The playlist name was invalid.
          </v-card-title>
          <v-card-title class="pt-0">
            Please try again.
          </v-card-title>
          <v-card-actions>
            <v-spacer />
            <v-btn
              v-on:click="invalidPlaylistName = false"
              color="green darken-2"
            >
              OK
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog
        v-model="deletePlaylistDialog"
        :max-width="$vuetify.breakpoint.xs ? '280' : '460'"
      >
        <v-card>
          <v-card-title class="dialog-card text-center">
            Are you sure you want to delete this playlist? This action cannot be
            undone.
          </v-card-title>
          <v-card-actions>
            <v-btn v-on:click="deletePlaylistDialog = false"> Cancel </v-btn>
            <v-spacer />
            <v-btn
              v-on:click="deletePlaylist"
              color="green darken-2"
              text-color="black"
              >OK</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-container>
    <v-divider />
  </v-container>
</template>

<script>
import { deletePlaylist } from "../../api/api";
import Router from "../../router";
import { emitUpdatedName } from "../../utils/emitUtils";
import { isPlaylistNameValid } from "../../utils/validationUtils.js";

export default {
  name: "PlaylistHeader",
  props: ["playlist"],
  data: () => {
    return {
      newPlaylistName: "",
      deletePlaylistDialog: false,
      editPlaylistDialog: false,
      invalidPlaylistName: false
    };
  },
  methods: {
    changePlaylistName: function() {
      if (!isPlaylistNameValid(this.newPlaylistName)) {
        this.editPlaylistDialog = false;
        this.invalidPlaylistName = true;
        return;
      }

      const newName = this.newPlaylistName;
      emitUpdatedName(this, newName);
      this.editPlaylistDialog = false;
    },
    deletePlaylist: async function() {
      const router = Router;
      await deletePlaylist(this.playlist.id).then(() => {
        this.deletePlaylistDialog = false;
        router.push("/");
      });
    }
  }
};
</script>

<style scoped>
.edit-btn {
  background-color: rgb(88, 86, 214);
}

.dialog-card {
  word-break: normal !important;
}
</style>
