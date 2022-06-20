<template>
  <div class="simple-keyboard">

  </div>
</template>

<script>
import Keyboard from "simple-keyboard";
import "simple-keyboard/build/css/index.css";

export default {
    data(){
        return{
            keyboardGame : this.$refs[null],
        }
    },

    async mounted() {
        this.keyboardGame = new Keyboard('simple-keyboard', {
            layout :{
                default: [
                    'q w e r t y u i o p',
                    'a s d f g h j k l',
                    '{enter} z x c v b n m {bksp}'
                ],
            },
            onKeyPress: this.onKeyPress,
        })
    },

    methods: {
        onKeyPress(button) {
            this.$emit('onKeyPress', button)
        }
    },

}
</script>

<style>

</style>