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
    "
  >
    {{ letter }}
  </div>
</template>

<script>
export default {
  props: {
    letter:{
        type: String,
        default: "",
    }
  },

  watch:{
    letter(newValue){
        this.$emit('update:letter', newValue)
    }
  }
};
</script>

<style>
</style>