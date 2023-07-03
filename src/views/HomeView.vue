<template>
  <div class="home">
    <div class="container d-flex align-items-center justify-content-center">
      <div class="row">
        <div class="col">
          <div class="card text-center cardOpacity">
            <div class="card-body">
              <h5 class="card-title title">Chombas Music Search</h5>
              <p class="card-text">
                Buscá información de tus canciones favoritas
              </p>
              <input class="form-control viceInput" type="text" placeholder="Nombre de la canción..."
                aria-label="default input example" v-model="userSongName" @keydown.enter="redirectToAbout" />
              <div>
                <button class="viceButton" @click="redirectToAbout">➨</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HomeView",
  data() {
    return {
      userSongName: ''
    };
  },
  methods: {
    redirectToAbout() {
      const songName = this.userSongName;
      if (songName === "") {
        alert("Ingrese una canción para buscar antes de darle al botón");
      }
      this.$router.push(`/about/${songName}`);
    }
  }
}
</script>

<style>
@font-face {
  font-family: "vice";
  src: url("@/assets/ViceCitySans-ItalicBold.otf");
}

.title {
  font-family: "vice";
  font-size: 48px;
  color: #528057;
}

.card-text {
  color: #314d34;
}

.card-body {
  padding-left: 48px;
  padding-right: 48px;
}

.viceInput {
  background-color: rgba(255, 255, 255, 0.5);
  border-color: #fe0252;
}

.viceButton {
  margin-top: 18px;
  padding-left: 24px;
  padding-right: 24px;
  border-radius: 8px;
  color: #7e0039;
  background-color: #fe0252;
  font-weight: bolder;
  font-size: 32px;
  -webkit-text-stroke-width: 0.2px;
  -webkit-text-stroke-color: black;
  border: none;
}

.cardOpacity {
  background-color: rgba(255,
      255,
      255,
      0.6);
  /* Cambia el último valor (0.7) para ajustar la opacidad */
}

body {
  background-image: url("https://wallpapercave.com/wp/wp2429567.jpg");
  background-size: fill;
}
</style>
