<template>
  <div class="row">
    <div class="col-12">
      <div class="about">
        <h4 class="title">{{ songName }}</h4>
        <div class="list-group-container">
          <div class="list-group m-5 list-group-size">
            <button class="list-group-item list-group-item-action list-group-item" aria-current="true">
              Resultados
            </button>
            <div class="d-flex justify-content-center" v-if="loading">
              <button type="button" class="list-group-item-action">
                <div class="spinner-border" style="width: 3rem; height: 3rem;" role=" status"></div>
              </button>
            </div>
            <button v-for="song in songs" :key="song.id" type="button" class="list-group-item list-group-item-action"
              @click="getSongId(song.id)">
              {{ song.title }} | {{ song['artist-credit'][0].name }} | {{ song.length / 1000 }} s.
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
@import url("https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css");
@import url('https://fonts.googleapis.com/css2?family=Asset&display=swap');

.list-group-item:first-child {
  background-color: rgb(30, 30, 30);
  color: rgb(255, 180, 40);
  font-weight: bold;
}

.list-group-container {
  display: flex;
  justify-content: center;
}

.list-group-size {
  max-width: 700px;
  width: 100%;
  height: auto;
}

.title {
  font-size: 40px;
  font-family: Asset, system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  color: whitesmoke;
}
</style>

<script>
import axios from "axios";
export default {
  data() {
    return {
      songs: [],
      songName: "",
      loading: true,
      songId: ""
    };
  },
  created() {
    this.getSongName();
  },
  methods: {
    getSongName() {
      const songName = this.$route.params.query;
      const endpoint = `https://musicbrainz.org/ws/2/recording?fmt=json&query=${songName}`;
      axios.get(endpoint)
        .then(response => {
          this.songs = response.data.recordings;
          this.songName = songName;
          this.loading = false;
          console.log(response.data);
        })
        .catch(error => {
          console.error(error);
        });
    },
    getSongId(id) {
      const selectedSong = this.songs.find(song => song.id === id);
      this.$router.push(`/song/${selectedSong.id}`);
    }
  }
}
</script>

