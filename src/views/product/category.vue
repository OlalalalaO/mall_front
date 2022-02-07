<template>
  <div>
    <el-switch
        v-model="value1"
        active-text="开启拖拽"
        inactive-text="关闭拖拽">
    </el-switch>
    <el-button type="danger" round>危险按钮</el-button>
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
          >
            Append
          </el-button>
          <el-button
              type="text"
              size="mini"
              @click="() => edit(data)"
          >
            Edit
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
      </span>


    </el-tree>
    <el-dialog title="添加分类" :visible.sync="dialogFormVisible">
      <el-form :model="category">
        <el-form-item label="分类名称" label-width=70px>
          <el-input v-model="category.name" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel()">取 消</el-button>
        <el-button type="primary" @click="addCategory()">确 定</el-button>
      </div>
    </el-dialog>


    <el-dialog title="编辑分类" :visible.sync="dialogEditFormVisible">
      <el-form :model="categoryData">
        <el-form-item label="分类名称" label-width=70px>
          <el-input v-model="categoryData.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="分类等级" label-width=70px>
          <el-input v-model="categoryData.sort" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="商品数量" label-width=70px>
          <el-input v-model="categoryData.productCount" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="计量单位" label-width=70px>
          <el-input v-model="categoryData.productUnit" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="图标地址" label-width=70px>
          <el-input v-model="categoryData.icon" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editCancel()">取 消</el-button>
        <el-button type="primary" @click="editCategory()">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "category",
  data() {
    return {
      value1: false,
      menus: [],
      dialogEditFormVisible: false,
      dialogFormVisible: false,
      category: {
        name: "",
        parentId: 0,
        catLevel: 0,
        sort: 0,
        showStatus: 1,
        productCount: 0
      },
      categoryData: {
        name: "",
        sort: 0,
        productCount: 0,
        icon: "",
        productUnit: "",

      },
      expanded: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  methods: {
    append(data) {
      this.dialogFormVisible = true;
      this.category.parentCid = data.catId;
      this.category.catLevel = data.catLevel * 1 + 1;
    },
    edit(data) {
      this.dialogEditFormVisible = true;
      // get request by axios
      this.$axios({
        method: "get",
        url: "http://localhost:8086/api/product/category/info/" + data.catId
      }).then(res => {
        console.log(res.data.category);
        this.categoryData = res.data.category;
      });

    },
    cancel() {
      this.dialogFormVisible = false;
      this.category = {
        name: "",
        parentId: 0,
        catLevel: 0,
        sort: 0,
        showStatus: 1,
        productCount: 0
      }
    },
    editCancel() {
      this.dialogEditFormVisible = false;
      this.categoryData = {
        name: "",
        sort: 0,
        productCount: 0,
        icon: "",
        productUnit: "",
      }
    },
    editCategory() {
      this.$axios({
        method: "post",
        url: "http://localhost:8086/api/product/category/update/",
        data: this.categoryData
      }).then(res => {
        if (res.data.code === 0) {
          let category = res.data.category;
          this.$message({
            message: "编辑成功",
            type: "success"
          });
          this.dialogEditFormVisible = false;
          this.getList();
          console.log(category.catId);
          this.expanded = [category.catId];
          this.categoryData = {
            name: "",
            sort: 0,
            productCount: 0,
            icon: "",
            productUnit: "",
          }
        } else {
          this.$message({
            message: "编辑失败",
            type: "error"
          });
        }
      });
    },
    addCategory() {
      this.$axios({
        method: "post",
        url: "http://localhost:8086/api/product/category/save",
        data: this.category
      }).then(res => {
        if (res.data.code === 0) {
          let category = res.data.category;
          this.dialogFormVisible = false;
          this.$message({
            message: "添加成功",
            type: "success"
          });
          this.getList()
          console.log(category.catId);
          this.expanded = [category.catId];
          this.category = {
            name: "",
            parentId: 0,
            catLevel: 0,
            sort: 0,
            showStatus: 1,
            productCount: 0
          }
        }
      })
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
