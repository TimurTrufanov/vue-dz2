<template>
  <div class="my-2">
    <button @click="decreaseAge" class="btn btn-primary">Уменьшить возраст</button>
    <button @click="increaseAge" class="btn btn-primary">Увеличить возраст</button>
  </div>
</template>

<script>
export default {
  name: "ButtonComponent",
  methods: {
    increaseAge() {
      this.$emit('increase-age');
    },
    decreaseAge() {
      this.$emit('decrease-age');
    }
  }
}
</script>

<style scoped>

</style>