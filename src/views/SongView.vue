<template>
    <div class="row m-5">
        <div class="col-sm-6 mb-3 mb-sm-0">
            <h4 class="title">{{ songName }}</h4>
            <div class="card border-primary">
                <div class="card-header">
                    <h5 class="card-title"><strong>Información de la canción</strong></h5>
                </div>
                <div class="card-body text-primary">
                    <div class="d-flex justify-content-center" v-if="loadingSongInfo">
                        <div class="spinner-border" style="width: 3rem; height: 3rem;" role=" status"></div>
                    </div>
                    <p class="card-text" v-if="!loadingSongInfo"><strong>Título: </strong>{{ songName }}</p>
                    <p class="card-text" v-if="!loadingSongInfo"><strong>Artista: </strong>{{ artistName }}</p>
                    <p class="card-text" v-if="!loadingSongInfo"><strong>Duración: </strong>{{ songLength / 1000 }} segundos
                    </p>
                </div>
            </div>
        </div>
        <div class="col-sm-6">
            <h4 class="title">{{ artistName }}</h4>
            <div class="card border-primary">
                <div class="card-header">
                    <h5 class="card-title"><strong>Información del artista</strong></h5>
                </div>
                <div class="card-body text-primary">
                    <div class="artistInfo">
                        <a href="#" class="btn btn-primary colorButton" @click="getArtistInfo()"
                            v-if="!loadingSongInfo && !loadingInfo">Ver información
                            del artista</a>
                        <div class="d-flex justify-content-center" v-if="loadingSongInfo">
                            <div class="spinner-border" style="width: 3rem; height: 3rem;" role=" status"></div>
                        </div>
                        <p class="card-text" v-if="loadingInfo"><strong>País: </strong>{{ countryArtist || "No encontrado"
                        }}
                        </p>
                        <p class="card-text" v-if="loadingInfo"><strong>Ciudad: </strong>{{ cityArtist || "No encontrado" }}
                        </p>
                        <p class="card-text" v-if="loadingInfo"><strong>Nacimiento: </strong>{{ begin || "No encontrado" }}
                        </p>
                        <p class="card-text" v-if="loadingInfo"><strong>Rating: </strong>{{ value || "No encontrado" }}</p>
                    </div>
                    <div>
                        <a href="#" class="btn btn-primary colorButton" @click="findAlbums" v-if="!loadingSongInfo">Ver
                            discografía
                            del artista ➨</a>
                    </div>
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
            songId: "",
            songName: "",
            songLength: 0,
            artistName: "",
            votesCount: 0,
            artistId: 0,
            loadingInfo: false,
            loadingSongInfo: true,
            loadingArtistInfo: true,
            countryArtist: "",
            cityArtist: "",
            begin: "",
            value: 0
        };
    },
    created() {
        this.getSongInfo();
    },
    methods: {
        getSongInfo() {
            const songId = this.$route.params.id;
            const endpoint = `https://musicbrainz.org/ws/2/recording/${songId}?inc=artist-credits+releases+tags+ratings&fmt=json`
            axios.get(endpoint)
                .then(response => {
                    this.songName = response.data.title;
                    this.songLength = response.data.length;
                    this.artistName = response.data['artist-credit'][0].artist.name;
                    this.votesCount = response.data.rating["votes-count"];
                    this.artistId = response.data["artist-credit"][0].artist.id;
                    this.loadingSongInfo = false;
                    console.log(response.data);
                })
                .catch(error => {
                    console.error(error);
                });
        },
        getArtistInfo() {
            const artistId = this.artistId;
            const endpoint = `https://musicbrainz.org/ws/2/artist/${artistId}?fmt=json&inc=aliases+tags+ratings`;
            axios.get(endpoint)
                .then(response => {
                    this.loadingInfo = true;
                    this.countryArtist = response.data.area.name;
                    this.cityArtist = response.data["begin-area"].name;
                    this.begin = response.data["life-span"].begin;
                    this.value = response.data.rating.value;
                    console.log(response.data);
                })
                .catch(error => {
                    console.log(error);
                })
        },
        findAlbums() {
            const artistId = this.artistId;
            this.$router.push(`/discography/${artistId}`);
        }
    }
};
</script>
<style scoped>
@font-face {
    font-family: "vice";
    src: url("@/assets/ViceCitySans-ItalicBold.otf");
}

.title {
    font-size: 48px;
    font-family: "vice";
    color: whitesmoke;
}

.artistInfo {
    margin-bottom: 20px;
}

.colorButton {
    background-color: #c6003e;
    border: 1px solid #9e0032;
}

.colorButton:hover {
    background-color: #a50d3d;
    border: 1px solid #78072b;
}
</style>
  