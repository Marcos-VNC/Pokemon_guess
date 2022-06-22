<template>
  <div class="simple-keyboard"></div>
</template>

<script>
import Keyboard from "simple-keyboard";
import "simple-keyboard/build/css/index.css";

export default {
  data() {
    return {
      keyboardGame: this.$refs[null],
    };
  },
  props: {
    guessedLetters: Object,
  },

  watch: {
    "guessedLetters": {
      handler(guessedLetters, prevGuessedLetters) {
        this.keyboardGame.addButtonTheme(
            guessedLetters.miss.join(" "),"miss"
        );
        this.keyboardGame.addButtonTheme(
            guessedLetters.found.join(" "),"found"
        );
        this.keyboardGame.addButtonTheme(
            guessedLetters.hint.join(" "),"hint"
        );
      },
      deep: true,
    },
  },

  async mounted() {
    this.keyboardGame = new Keyboard("simple-keyboard", {
      layout: {
        default: [
          "q w e r t y u i o p",
          "a s d f g h j k l",
          "{enter} z x c v b n m {bksp}",
        ],
      },
      onKeyPress: this.onKeyPress,
    });
  },

  methods: {
    onKeyPress(button) {
      this.$emit("onKeyPress", button);
    },
  },
};
</script>

<style> 


div.miss {
    @apply bg-gray-500 !important;
    @apply text-white;
}

div.found {
    @apply bg-green-600 !important;
    @apply text-white;
}

div.hint:not(.found){
    @apply bg-yellow-500 !important;
    @apply text-white;
}


.hg-theme-default .hg-button{
  background-color:#defffb;
  font-size: 20px;
}

.hg-theme-default{
  background-color: transparent;
}

</style>