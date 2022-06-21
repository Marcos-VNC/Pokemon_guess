<template>
  <div class="grid max-w-xs grid-cols-5 gap-1 mx-auto mb-1">
    <letter-box
      v-for="i in 5"
      :key="i"
      :letter="value[i - 1]"
      :color="colors[i - 1]"
      :tempColor.sync="temp_color"
    />
  </div>
</template>

<script>
import LetterBox from "./LetterBox.vue";


export default {
  components: { LetterBox },

  props: {
    value: String,
    solution: String,
    submitted: Boolean,
    tempValue: String,
  },
  data() {
    return {
      localValue: "",
      colors: ["", "", "", "", "", ""],
      temp_color: "",
    };
  },

  watch: {
    tempValue(newValue) {
      this.$emit("update:tempValue", newValue);
      // console.log(this.value)
      // console.log('temp value', this.tempValue)
    },
    submitted(submitted, prevSubmitted) {
      if (this.submitted) {
        let s = this.solution;
        let v = this.value;

        let temp = ["gray", "gray", "gray", "gray", "gray"];
        let letterPool = [];
        for (let i = 0; i < 5; i++) {
          if (s.charAt(i) == v.charAt(i)) {
            //se o valor bater
            temp[i] = "green";
          } else {
            letterPool.push(s.charAt(i));
          }
        }
        for (let i = 0; i < 5; i++) {
          if (temp[i] == "gray") {
            if (letterPool.indexOf(v.charAt(i)) != -1) {
              letterPool.splice(letterPool.indexOf(v.charAt(i)), 1);
              temp[i] = "yellow";
            }
          }
          this.colors[i] = temp[i];
          this.temp_color = temp[i];
          new Promise((resolve) => setTimeout(resolve, 933));
        }
      }
    },
  },
};
</script>

<style>
</style>