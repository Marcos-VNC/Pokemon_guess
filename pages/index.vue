<template>
  <div class="flex flex-col h-screen max-w-md mx-auto justify-evenly">
    <div>
      <word-row
        v-for="(guess, i) in guesses"
        :key="i"
        :value="guess"
        :tempValue.sync="temp_resp"
        :solution="solution"
        :submitted="i < currentGuessIndex"
      />
    </div>
    <simple-keyboard @onKeyPress="handleInput" />
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
      solution: "books",
      guesses: ["", "", "", "", "", ""],
      temp_resp: "",
      currentGuessIndex: 0,
    };
  },

  async mounted() {
    await window.addEventListener("keyup", (e) => {
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

  created() {},

  methods: {
    handleInput(key) {
      if (this.currentGuessIndex >= 6) {
        return;
      }
      const currentGuess = this.guesses[this.currentGuessIndex];

      if (key == "{enter}") {
        //Envia a tentativa
      } else if (key == "{bksp}") {
        //Remove a ultima letra
        this.guesses[this.currentGuessIndex] = currentGuess.slice(0, -1);
        this.temp_resp = this.guesses[this.currentGuessIndex]

      } else if (currentGuess.length < 5) {
        //Adiciona a letra

        const alphaRegex = /[a-zA-Z]/;
        if (alphaRegex.test(key)) {
          this.guesses[this.currentGuessIndex] += key;
          this.temp_resp += key;
        }
        // console.log(this.guesses[0])
      }
    },
  },
};
</script>

<style>
</style>
