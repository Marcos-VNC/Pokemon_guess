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
      <div class="flex flex-col h-screen max-w-md mx-auto justify-evenly">
        <div v-if="iniciar">
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
        </div>
        <p v-if="wonGame" class="text-center">Congrats</p>
        <p v-else-if="lostGame" class="text-center">Cry</p>
        <button class="init" v-if="!iniciar" @click="iniciarGame">
          INICIAR
        </button>
      </div>
    </div>
    <div class="side-bar">
      <img src="../static/menu.png" class="menu" />
      <div class="social-links">
        <img src="../static/socials/fb.png" alt="" />
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

  created() {
    const id = this.aleatorio(1, 250);
    this.$axios.$get(`/pokemon/${id}`).then((response) => {
      console.log(response.name);
      this.letterLength = response.name.length;
      this.solution = response.name;
      this.myclass = `grid-cols-${this.letterLength}`;
    });
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

.hero {
  width: 100%;
  height: 100vh;
  background-image: url("../static/pokemon_bg2.png");
  background-size: cover;
  background-position: center;
  position: relative;
  overflow: hidden;
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
  /* position: absolute; */
  /* top: 50%;
  left: 35%;
  transform: translateY(-50%);
  z-index: 2; */
}

h1 {
  font-size: 80px;
  margin: 10px 0 30px;
  line-height: 80px;
}

.side-bar {
  width: 50px;
  height: 100vh;
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
</style>
