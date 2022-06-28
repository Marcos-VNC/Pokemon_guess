<template>
  <div
    class="
      col-span-1
      flex
      items-center
      justify-center
      h-16
      uppercase
      border-2 border-gray-200
      transition-all duration-300
      divBorder
    "
    :class="{
      'border-gray-500 bg-gray-500 text-white': color == 'gray',
      'border-green-600 bg-green-600 text-white': color == 'green',
      'border-yellow-500 bg-yellow-500 text-white': color == 'yellow',
    }"
  >
    {{ letter }}
  </div>
</template>

<script>
export default {
  props: {
    letter: {
      type: String,
      default: "",
    },
    color: {
      type: String,
      default: "",
    },
    tempColor: {
      type: String,
      default: "",
    },
  },

  watch: {
    letter(newValue) {
      this.$emit("update:letter", newValue);
    },
    color(newValue) {
      this.$emit("update:color", newValue);
      console.log('temp')
    },
    tempColor(newValue) {
      this.$emit("update:tempColor", newValue);
      // console.log(this.value)
      // console.log('temp value', this.tempValue)
    },
  },
};
</script>

<style>

.divBorder{
  border-color: rgb(58, 58, 58);
}
</style>