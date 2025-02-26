<template>
  <v-container class="px-0 mx-0">
    <v-subheader class="subtitle-1 font-weigth">Playlists</v-subheader>
    <v-container v-if="!isConnected">
      <v-card>
        <v-card-text>
          Login or sign up to access your playlists
        </v-card-text>
      </v-card>
    </v-container>
    <v-container> </v-container>
    <v-container
      v-if="initialLoading && isConnected"
      style="width: 225px; height: 150px;"
    >
      <v-progress-circular
        class="mt-6"
        style="left: 38%;"
        size="50"
        :indeterminate="true"
        color="rgb(88,86,214)"
      ></v-progress-circular>
    </v-container>
    <vue-custom-scrollbar
      v-if="isConnected && !initialLoading"
      class="scroll-area"
      :settings="scrollbarSettings"
    >
      <v-list dense class="px-0 mx-0 overflow-y-auto">
        <v-list-item-group>
          <v-list-item
            v-for="playlist in playlists"
            :key="playlist.id"
            :to="`/playlist/${playlist.id}`"
          >
            <v-list-item-content class="py-0 my-0">
              <v-list-item-title
                class="subtitle-1 py-0 my-0"
                v-text="playlist.name"
              />
            </v-list-item-content>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </vue-custom-scrollbar>
    <add-playlist v-if="isConnected" />
  </v-container>
</template>

<script>
import { getPlaylists } from "../../api/api.js";
import vueCustomScrollbar from "vue-custom-scrollbar";
import AddPlaylistButton from "../AddPlaylistButton.vue";
import { getCurrentUserTokenInfo, isConnected } from "../../api/api";

export default {
  name: "PlaylistBar",
  components: {
    vueCustomScrollbar,
    "add-playlist": AddPlaylistButton
  },
  data: function() {
    return {
      playlists: [],
      initialLoading: false,
      isConnected: false,
      scrollbarSettings: {
        minScrollbarLength: 40,
        maxScrollbarLength: 80
      }
    };
  },
  created() {
    if (isConnected()) {
      this.initialLoading = true;
      this.getPlaylists();
    }
  },
  updated() {
    if (isConnected()) {
      this.getPlaylists();
    } else {
      this.isConnected = false;
    }
  },
  methods: {
    getPlaylists: async function() {
      const playlists = await getPlaylists();
      if (playlists !== null) {
        this.isConnected = true;
        this.initialLoading = false;
        const userInfo = await getCurrentUserTokenInfo();
        if (userInfo !== null) {
          this.playlists = playlists.filter(
            playlist => playlist.owner.id === userInfo.id
          );
          this.$emit("playlists-ready", this.playlists);
        }
      } else {
        this.initialLoading = true;
        this.isConnected = false;
      }
    }
  }
};
</script>

<style scoped>
v-list__tile {
  padding: 0;
  margin: 0;
}
.scroll-area {
  position: relative;
  margin: auto;
  width: 225px;
  max-height: 300px;
}
</style>
