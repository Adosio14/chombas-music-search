<template>
  <div class="discography">
    <div class="container d-flex justify-content-center">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title title">Discografía de {{ artistName }}</h5>
          <h6 class="card-text">{{ artistName }} tiene un total de {{ albums.length }} álbumes</h6>
          <p v-if="loading" class="card-text">
            Por favor esperá mientras reunimos las carátulas de los albumes
          </p>
          <div v-if="!loading" class="row">
            <div
              v-for="(album, index) in albums"
              :key="album.id"
              class="col-xl-4 col-md-6 col-sm-12"
              id="albumCard"
            >
              <div class="card p-2">
                <div v-if="coverPhotos[index] != undefined" id="caratula">
                  <img
                    :src="coverPhotos[index]"
                    class="card-img-top"
                    alt="..."
                  />
                </div>
                <div v-else><img src="https://s.pngkit.com/png/small/9-92371_cd-case-template-png-vector-royalty-free-blank.png" alt=""></div>
                <div class="card-body">
                  <h5 class="card-title">{{ album.title }}</h5>
                  <h6 class="card-subtitle mb-2 text-muted">
                    {{ album.date }} • {{ album.status }}
                  </h6>
                  <p class="card-text">
                    <strong>Tipo de empaque:</strong>
                    {{
                      album.packaging == null ? "Desconocido" : album.packaging
                    }}
                  </p>
                  <p class="card-text">
                    <strong>País de lanzamiento:</strong>
                    {{ album.country == null ? "Desconocido" : album.country }}
                  </p>
                  <p class="card-text">
                    <strong>Calidad:</strong>
                    {{ album.quality == null ? "Desconocido" : album.quality }}
                  </p>
                </div>
              </div>
            </div>
          </div>
          <div
            v-else
            class="spinner-border text-info"
            id="spinner"
            role="status"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      loading: false,
      albums: [],
      coverPhotos: [],
      artistName: ""
    };
  },
  name: "DiscographyView",
  components: {},
  created() {
    this.getAlbums();
  },
  methods: {
    async getAlbums() {
        const artistId = this.$route.params.id
    this.getArtistName(artistId)
      this.loading = true;
      let url = `https://musicbrainz.org/ws/2/release?artist=${artistId}&fmt=json&type=album`;
      try {
        const response = await axios.get(url);
        console.log(response.data)
        this.albums = response.data.releases;
        for (let album of this.albums) {
          let photo = await this.getCover(album.id);
          this.coverPhotos.push(photo);
        }
      } catch {
        console.log("aia");
      }
      this.loading = false;

    },
    async getCover(releaseId) {
      (releaseId);
      let url = `https://coverartarchive.org/release/${releaseId}`;

      try {
        let response = await axios.get(url);
        return response.data.images[0].image;
      } catch {
        console.log("error foto");
      }
    },
    async getArtistName(artistId){
        // let artistId = artistId;
        const url = `https://musicbrainz.org/ws/2/artist/${artistId}?fmt=json&inc=aliases+tags+ratings`;
        try {
        const response = await axios.get(url);
        this.artistName = response.data.name
      } catch {
        console.log("aia");
      }
    }
  },
};
</script>
<style scoped>
.title {
  font-family: "vice";
  font-size: 48px;
  color: #528057;
}
#caratula {
  margin: 12px;
  overflow: hidden;
  border-bottom-right-radius: 24px;
  border-top-left-radius: 24px;
}
.card-text {
  color: #314d34;
}
#albumCard {
  margin-top: 12px;
}
</style>
