<template>
  <div>
    <div>Имя: {{ user.name }}</div>
    <div>Возраст: {{ user.age }}</div>
  </div>
</template>

<script>
export default {
  name: "UserComponent",
  props: ['user']
}
</script>

<style scoped>

</style>