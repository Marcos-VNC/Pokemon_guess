<template>
  <div class="hero">
    <div class="nav-bar">
      <div class="nav-logo">
        <img src="../static/logo.png" class="logo" />
        <p>Pokemon Guess</p>
      </div>
      <button type="button">Sign Up</button>
    </div>
    <div class="content">
      <div class="flex flex-col h-screen max-w-md mx-auto justify-evenly game">
        <div style="margin-bottom: auto" v-if="iniciar">
          <word-row
            v-for="(guess, i) in guesses"
            :key="i"
            :value="guess"
            :tempValue.sync="temp_resp"
            :solution="solution"
            :submitted="i < currentGuessIndex"
            :letterLength="letterLength"
            :customClass="myclass"
          />
          <simple-keyboard
            @onKeyPress="handleInput"
            :guessedLetters="guessedLetters"
          />
          <br />

          <div v-if="currentGuessIndex >= 1" class="flex flex-wrap text-center">
            <div class="w-1/3 ml-auto bg-gray-500 h-12 dica">
              <p>Type</p>
            </div>
            <div class="w-1/3 mr-auto h-12 dica">
              <div v-if="tipos[1] != null" v-bind:class="coresTipos[0]">
                <p>{{ tipos[0] }}</p>
              </div>
              <div v-if="tipos[1] != null" v-bind:class="coresTipos[1]">
                <p>{{ tipos[1] }}</p>
              </div>

              <div v-else v-bind:class="coresTipos[0]">
                <p>{{ tipos[0] }}</p>
              </div>
            </div>
          </div>
          <div v-if="currentGuessIndex >= 2" class="flex flex-wrap text-center">
            <div class="w-1/3 ml-auto bg-gray-500 h-12 dica">
              <p>Color</p>
            </div>
            <div class="w-1/3 mr-auto bg-gray-400 h-12 dica">
              <p>{{ cor }}</p>
            </div>
          </div>
          <div v-if="currentGuessIndex >= 3" class="flex flex-wrap text-center">
            <div class="w-1/3 ml-auto bg-gray-500 h-12 dica">
              <p>Generation</p>
            </div>
            <div class="w-1/3 mr-auto bg-gray-400 h-12 dica">
              <p>{{ geracao }}</p>
            </div>
          </div>
          <div v-if="currentGuessIndex >= 4" class="flex flex-wrap text-center">
            <div class="w-1/3 ml-auto bg-gray-500 h-12 dica">
              <p>Habitat</p>
            </div>
            <div class="w-1/3 mr-auto bg-gray-400 h-12 dica">
              <p>{{ habitat }}</p>
            </div>
          </div>
          <div v-if="currentGuessIndex >= 5" class="dica">
            <div class="bg-gray-500 h-12 dica desc">
              <p>Description</p>
            </div>
          </div>
          <div v-if="currentGuessIndex >= 5" class="dica-text">
            <div class="bg-gray-400 h-12 dica desc-text scroll">
              {{ descricao }}
            </div>
          </div>
          <br><br>
          <div v-if="wonGame" class="pokemon">
            <div class="wrapper">
              <a href="#modalbox">Ganhou, Ver pokemon</a>
              <a href="">New game</a>
            </div>

            <div id="modalbox" class="modal">
              <div class="bg-gray-400 modalcontent">
                <h2>{{ pokemon.name }}</h2>
                <img :src="spriteOficial" alt="" />
                <a href="#" class="modalclose">&times;</a>
              </div>
            </div>
          </div>

          <div v-else-if="currentGuessIndex >= 5" class="pokemon">
            <div v-if="lostGame" class="wrapper">
              <a href="#modalbox">Perdeu, Ver pokemon</a>
              <a href="">New game</a>
            </div>
            <div v-else>
              <img :src="sprite" alt="" />
            </div>

            <div id="modalbox" class="modal">
              <div class="bg-gray-400 modalcontent">
                <h2>{{ pokemon.name }}</h2>
                <img :src="spriteOficial" alt="" />
                <a href="#" class="modalclose">&times;</a>
              </div>
            </div>
          </div>

          <!-- <p v-if="wonGame" class="text-center">Congrats</p>
          <p v-else-if="lostGame" class="text-center">Cry</p> -->
        </div>

        <button class="init" v-if="!iniciar" @click="iniciarGame">
          INICIAR
        </button>
      </div>
    </div>
    <div class="side-bar">
      <img src="../static/menu.png" class="menu" />
      <div class="social-links">
        <a href="https://github.com/Marcos-VNC/Pokemon_guess"><img src="../static/socials/github.png" alt="" /></a> 
        <img src="../static/socials/ig.png" alt="" />
        <img src="../static/socials/tw.png" alt="" />
      </div>
      <div class="useful-links">
        <img src="../static/socials/share.png" alt="" />
        <img src="../static/socials/info.png" alt="" />
      </div>
    </div>

    <div class="pokebolls">
      <div class="rotate"><img src="../static/pokebola.png" /></div>
      <div class="rotate"><img src="../static/pokebola.png" /></div>
      <div class="rotate"><img src="../static/pokebola.png" /></div>
      <div class="rotate"><img src="../static/pokebola.png" /></div>
      <div class="rotate"><img src="../static/pokebola.png" /></div>
      <div class="rotate"><img src="../static/pokebola.png" /></div>
    </div>
  </div>
</template>


<script>
import WordRow from "~/components/WordRow.vue";
import SimpleKeyboard from "~/components/SimpleKeyboard.vue";

export default {
  components: {
    SimpleKeyboard,
    WordRow,
  },
  data() {
    return {
      solution: "",
      letterLength: 0,
      guesses: ["", "", "", "", "", ""],
      temp_resp: "",
      currentGuessIndex: 0,
      guessedLetters: {
        miss: [],
        found: [],
        hint: [],
      },
      myclass: "",
      iniciar: false,
      pokemon: {},
      tipos: [],
      coresTipos: [],
      altura: "",
      cor: "",
      descricao: "",
      geracao: "",
      habitat: "",
      sprite: "",
      spriteOficial: "",
    };
  },

  async mounted() {
    window.addEventListener("keyup", (e) => {
      e.preventDefault();
      let key =
        e.keyCode == 13
          ? "{enter}"
          : e.keyCode == 8
          ? "{bksp}"
          : String.fromCharCode(e.keyCode).toLowerCase();

      this.handleInput(key);
    });
  },

  async created() {
    const id = this.aleatorio(1, 250);
    await this.$axios.$get(`/pokemon/${id}`).then((response) => {
      console.log(response.name);
      this.letterLength = response.name.length;
      this.solution = response.name;
      this.pokemon = response;
      this.sprite = response.sprites.front_default;
      this.spriteOficial = response.sprites.other.home.front_default;
      this.myclass = `grid-cols-${this.letterLength}`;

      this.$axios
        .$get(`/pokemon-species/${response.species.name}`)
        .then((response_specie) => {
          console.log(response_specie.color.name);
          this.cor = response_specie.color.name;
          this.descricao = response_specie.flavor_text_entries[0].flavor_text;
          this.habitat = response_specie.habitat.name;
          this.geracao = response_specie.generation.name;
        });

      for (let i = 0; i < response.types.length; i++) {
        this.tipos.push(response.types[i].type.name);

        if (response.types[i].type.name == "fire") {
          this.coresTipos.push(`bg-red-400 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "water") {
          this.coresTipos.push(`bg-blue-400 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "grass") {
          this.coresTipos.push(`bg-green-500 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "psychic") {
          this.coresTipos.push(`bg-pink-600 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "fairy") {
          this.coresTipos.push(`bg-pink-700 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "poison") {
          this.coresTipos.push(`bg-purple-800 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "ground") {
          this.coresTipos.push(`bg-yellow-700 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "rock") {
          this.coresTipos.push(`bg-yellow-800 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "electric") {
          this.coresTipos.push(`bg-yellow-300 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "fighting") {
          this.coresTipos.push(`bg-red-800 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "normal") {
          this.coresTipos.push(`bg-gray-400 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "bug") {
          this.coresTipos.push(`bg-green-700 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "ice") {
          this.coresTipos.push(`bg-blue-200 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "flying") {
          this.coresTipos.push(`bg-blue-100 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "ghost") {
          this.coresTipos.push(`bg-indigo-800 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "dark") {
          this.coresTipos.push(`bg-gray-800 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "dragon") {
          this.coresTipos.push(`bg-teal-400 w-1/2 mr-auto h-12 dica`);
        } else if (response.types[i].type.name == "steel") {
          this.coresTipos.push(`bg-gray-500 w-1/2 mr-auto h-12 dica`);
        }
      }
    });
    console.log(this.coresTipos);
    console.log(this.tipos);
    console.log(this.pokemon);
    // for (let i = 0; i < pokemon.types.length; i++) {
    //   const element = array[i];
    // }
  },

  methods: {
    handleInput(key) {
      if (this.currentGuessIndex >= 6 || this.wonGame) {
        return;
      }
      const currentGuess = this.guesses[this.currentGuessIndex];

      if (key == "{enter}") {
        //Envia a tentativa
        if (currentGuess.length == this.letterLength) {
          this.currentGuessIndex++;
          for (var i = 0; i < currentGuess.length; i++) {
            let c = currentGuess.charAt(i);
            if (c == this.solution.charAt(i)) {
              this.guessedLetters.found.push(c);
            } else if (this.solution.indexOf(c) != -1) {
              this.guessedLetters.hint.push(c);
            } else {
              this.guessedLetters.miss.push(c);
            }
          }
        }
      } else if (key == "{bksp}") {
        //Remove a ultima letra
        this.guesses[this.currentGuessIndex] = currentGuess.slice(0, -1);
        this.temp_resp = this.guesses[this.currentGuessIndex];
      } else if (currentGuess.length < this.letterLength) {
        //Adiciona a letra

        const alphaRegex = /[a-zA-Z]/;
        if (alphaRegex.test(key)) {
          this.guesses[this.currentGuessIndex] += key;
          this.temp_resp += key;
        }
        // console.log(this.guesses[0])
      }
    },

    aleatorio(min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min);
    },
    iniciarGame() {
      this.iniciar = true;
    },
  },

  computed: {
    wonGame() {
      if (this.guesses[this.currentGuessIndex - 1] === this.solution) {
        return true;
      }
    },

    lostGame() {
      if (!this.wonGame && this.currentGuessIndex >= 6) {
        return true;
      }
    },
  },
};
</script>

<style>
@font-face {
  font-family: pokemonFont;
  src: url(../fonts/Pokemon_Solid.ttf);
}

* {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
}

::-webkit-scrollbar {
  display: none;
}

.hero {
  width: 100%;
  height: 100vh;
  background-image: url("../static/pokemon_bg2.png");
  background-size: cover;
  background-position: center;
  position: relative;
  overflow: hidden;
  overflow-x: hidden;
  overflow-y: auto;
  text-align: justify;
  scrollbar-width: none;
}

.logo {
  width: 100px;
  cursor: pointer;
}

.nav-bar {
  width: 85%;
  height: 15%;
  margin: auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.nav-logo {
  color: white;
  font-size: 42px;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}

.nav-logo p {
  cursor: default;
  font-family: pokemonFont;
  margin-bottom: 20px;
}

button {
  color: #fbfcfd;
  background: transparent;
  border-radius: 20px;
  outline: none;
  cursor: pointer;
}

.init {
  position: relative;
  display: inline-block;
  padding: 15px 2px;
  color: #2196f3;
  text-transform: uppercase;
  letter-spacing: 4px;
  overflow: hidden;
  transition: 0.2s;
}

.init:hover {
  color: #2196f3;
  background: #255784;
  box-shadow: 0 0 10px #2196f3, 0 0 40px #2196f3, 0 0 80px #2196f3;
}

.content {
  color: #fbfcfd;
  margin-top: 100px;
  /* position: absolute; */
  /* top: 50%;
  left: 35%;
  transform: translateY(-50%);
  z-index: 2; */
}

.game {
  margin-top: 100px;
}

h1 {
  font-size: 80px;
  margin: 10px 0 30px;
  line-height: 80px;
}

.side-bar {
  width: 50px;
  height: 140vh;
  background: linear-gradient(#212121, #000000);
  /* background: linear-gradient(#338545, #000000); */
  position: absolute;
  right: 0;
  top: 0;
}

.menu {
  display: block;
  width: 25px;
  margin: 40px auto 0;
  cursor: pointer;
}

.social-links img,
.useful-links img {
  width: 25px;
  margin: 5px auto;
  cursor: pointer;
}

.social-links {
  width: 50px;
  text-align: center;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}

.useful-links {
  width: 50px;
  text-align: center;
  position: absolute;
  bottom: 30px;
}

.pokebolls .rotate {
  width: 50px;
  animation: pokeboll 7s linear infinite;
  pointer-events: none;
}

.pokebolls .rotate img {
  width: 50px;
  animation: rotating 7s linear infinite;
}
.pokebolls {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  position: absolute;
  bottom: -60px;
  /* top: 50%;
  left: 35%;*/
  /* transform: translateY(-50%);
  z-index: 2; */
  padding-right: 60px;
  padding-left: 50px;
}

@keyframes pokeboll {
  0% {
    transform: translateY(0);
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
  70% {
    opacity: 1;
  }
  100% {
    transform: translateY(-70vh);
    opacity: 0;
  }
}

@keyframes rotating {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.pokebolls .rotate:nth-child(1) {
  animation-delay: 2s;
  width: 30px;
}
.pokebolls .rotate:nth-child(2) {
  animation-delay: 1s;
}
.pokebolls .rotate:nth-child(3) {
  animation-delay: 3s;
  width: 33px;
}
.pokebolls .rotate:nth-child(4) {
  animation-delay: 4.5s;
}
.pokebolls .rotate:nth-child(5) {
  animation-delay: 6s;
  width: 28px;
}
.pokebolls .rotate:nth-child(6) {
  animation-delay: 3s;
  width: 40px;
}

.dica {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 30px;
}

.dica-text {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 30px;
}

.dica div {
  height: 30px;
}
.dica-text div {
  margin-top: auto;
  height: 60px;
}

.desc {
  width: 66.7%;
}

.desc-text {
  margin-top: 5px;
  width: 66.7%;
}

.pokemon {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 0;
}

.pokemon img {
  width: 250px;
  height: 250px;
}

.scroll {
  padding: 1px;
  overflow-x: auto;
  /* overflow-y: hidden; */
  overflow: auto;
  white-space: unset;
  height: 150px;
}

/* MODAL */
.wrapper a {
  display: inline-block;
  text-decoration: none;
  background-color: #fff;
  padding: 15px;
  border-radius: 3px;
  text-decoration: uppercase;
  color: #585858;
}

.modal {
  position: absolute;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(77, 77, 77, 0.7);
  transition: all 0.4s;
  visibility: hidden;
  opacity: 0;
}

.modal:target {
  visibility: visible;
  opacity: 1;
}

.modalcontent {
  position: absolute;
  background: rgba(77, 77, 77, 1);
  width: 300px;
  max-width: 90%;
  padding: 1em 2em;
  border-radius: 4px;
  display: flex;
  flex-direction:column;
  justify-content: center;
  align-items: center;
}

.modalcontent h2{
  font-size: 40px;
}

.modalclose {
  position: absolute;
  top: 0;
  right: 15px;
  color: #ffffff;
  text-decoration: none;
  font-size: 36px;
}

/* .scroll ::-webkit-scrollbar {
  display: auto;
} */
/* .descricao {
  font-size: 8px;
} */

@media screen and (max-width: 600px) {
  .game {
    margin: 0em 4em;
    margin-left: 10px;
  }
}
</style>
