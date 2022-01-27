<template>
  <div>
    <el-tree :data="menus" :props="defaultProps"></el-tree>
  </div>
</template>

<script>
export default {
  name: "category",
  data() {
    return {
      menus:[],
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  created() {

    this.$axios({
      method: 'get',
      url: 'http://localhost:8086/api/product/category/list/tree'
    }).then(res => {
      this.menus = res.data
    })
  }
}
</script>

<style scoped>

</style>
