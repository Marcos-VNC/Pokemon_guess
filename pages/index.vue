<template>
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
    <button v-if="!iniciar" @click="iniciarGame">INICIAR</button>
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
      this.iniciar = true
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
</style>
