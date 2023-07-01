<template>
    <div class="row m-5">
        <div class="col-sm-6 mb-3 mb-sm-0">
            <div class="card">
                <div class="card-body">
                    <h5 class="card-title"><strong>Información de la canción</strong></h5>
                    <p class="card-text"><strong>Título: </strong>{{ songName }}</p>
                    <p class="card-text"><strong>Artista: </strong>{{ artistName }}</p>
                    <p class="card-text"><strong>Duración: </strong>{{ songLength / 1000 }} segundos</p>
                    <p class="card-text"><strong>Rating: </strong>{{ votesCount }}</p>
                </div>
            </div>
        </div>
        <div class="col-sm-6">
            <div class="card">
                <div class="card-body">
                    <h5 class="card-title">{{ artistName }}</h5>
                    <a href="#" class="btn btn-primary" @click="getArtistInfo()" v-if="!loadingInfo">Ver información del
                        artista</a>
                    <p class="card-text" v-if="loadingInfo"><strong>País: </strong>{{ countryArtist }}</p>
                    <p class="card-text" v-if="loadingInfo"><strong>Nacimiento: </strong>{{ begin }}</p>
                    <p class="card-text" v-if="loadingInfo"><strong>Votos: </strong>{{ value }}</p>
                    <br />
                    <br />
                    <a href="#" class="btn btn-primary" @click="findAlbums">Ver discografía del artista</a>
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
            countryArtist: "",
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
                    console.log(this.artistId)
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
                    this.countryArtist = response.data.country;
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
  