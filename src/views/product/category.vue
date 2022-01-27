<template>

</template>

<script>
export default {
  name: "category",
  created() {
    this.$axios({
      method: 'get',
      url: 'http://localhost:8086/api/product/category/'
    })
  }
}
</script>

<style scoped>

</style>
