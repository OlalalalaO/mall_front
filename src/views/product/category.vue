<template>
  <div>
    <el-tree :data="menus"
             :props="defaultProps"
             :expand-on-click-node="false"
             :default-expanded-keys="expanded"
             show-checkbox
             node-key="catId">
      <span class="custom-tree-node"
            slot-scope="{ node, data } ">
        <span>{{ node.label }} </span>
        <span>
          <el-button
              type="text"
              size="mini"
              @click="() => append(data)"
              v-if="node.level<=2"
          >
            Append
          </el-button>
          <el-button
              type="text"
              size="mini"
              @click="() => remove(node, data)"
              v-if="node.childNodes.length===0"
          >
            Delete
          </el-button>
        </span>
      </span></el-tree>
  </div>
</template>

<script>
export default {
  name: "category",
  data() {
    return {
      menus: [],
      expanded: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  methods: {
    append(data) {

    },
    remove(node, data) {

      this.$axios({
        method: 'post',
        url: 'http://localhost:8086/api/product/category/delete',
        data: [data.catId]
      }).then(res => {
        this.$message({
          message: '删除成功！',
          type: 'success'
        });
        this.expanded = [node.data.parentCid]
        this.getList()
      })
    },
    getList() {
      this.$axios({
        method: 'get',
        url: 'http://localhost:8086/api/product/category/list/tree'
      }).then(res => {
        this.menus = res.data
      })
    }
  },
  created() {
    this.getList();
  }
}
</script>

<style scoped>

</style>
