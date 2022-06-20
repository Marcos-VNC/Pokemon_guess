<template>
  <div class="grid max-w-xs grid-cols-5 gap-1 mx-auto mb-1">
    <letter-box v-for="i in 5" :key="i" :letter="value[i - 1]" />

  </div>
</template>

<script>
import LetterBox from "./LetterBox.vue";

export default {
  components: { LetterBox },
  props: ["value", "solution", "submitted", "tempValue"],
  data() {
    return {
      localValue: "",
    };
  },

  watch: {
    tempValue(newValue) {
      this.$emit("update:tempValue", newValue);
        // console.log(this.value)
        // console.log('temp value', this.tempValue)
    },
  },
};
</script>

<style>
</style>