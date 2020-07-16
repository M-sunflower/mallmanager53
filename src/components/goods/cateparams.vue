<template>
    <el-card>
      <my-bread level1="商品管理" level2="分类参数"></my-bread>
      <el-alert class="alert" title="只允许为第三级分类设置参数" type="error" center show-icon></el-alert>
      <el-form label-position="left" label-width="80px" style="height: 400px; overflow: auto">
        <el-form-item label="商品分类">
          <el-cascader
            v-model="selectedOptions"
            :options="options"
            :props="defaultProps"
            @change="handleChange"
            :show-all-levels="false"
            clearable>
          </el-cascader>
        </el-form-item>
        <el-tabs v-model="activeName" type="card" @tab-click="handleClick">
          <el-tab-pane label="动态参数" name="first">
            <el-button type="danger">设置动态参数</el-button>
            <el-table
              :data="arrDyparams"
              style="width: 100%">
              <el-table-column type="expand" label="#">
                <template slot-scope="scope">
                  <el-tag :key="tag" v-for="tag in scope.row.attr_vals" closable
                    :disable-transitions="false" @close="handleClose(scope.row,tag)">
                    {{tag}}
                  </el-tag>
                  <el-input class="input-new-tag" v-if="inputVisible" v-model="inputValue"
                    ref="saveTagInput" size="small" @keyup.enter.native="handleInputConfirm(scope.row)"
                    @blur="handleInputConfirm(scope.row)">
                  </el-input>
                  <el-button v-else class="button-new-tag" size="small" @click="showInput">+ New Tag</el-button>
                </template>
              </el-table-column>
              <el-table-column label="属性名称" prop="attr_name"></el-table-column>
              <el-table-column label="操作" >
                <template slot-scope="scope">
                  <el-button @click="showEditDia(scope.row)" size="mini" plain type="primary" icon="el-icon-edit" circle></el-button>
                  <el-button @click="showDeleteBox(scope.row.id)" size="mini" plain type="danger" icon="el-icon-delete" circle></el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="静态参数" name="second">配置管理</el-tab-pane>
        </el-tabs>
      </el-form>
    </el-card>
</template>

<script>
export default {
  name: 'cateparams',
  data () {
    return {
      selectedOptions: [],
      options: [],
      defaultProps: {
        label: 'cat_name',
        children: 'children',
        value: 'cat_id',
        expandTrigger: 'hover'
      },
      arrDyparams: [],
      activeName: 'first',
      inputVisible: false,
      inputValue: ''
    }
  },
  created () {
    this.getGoodscate()
  },
  methods: {
    async handleChange () {
      if (this.selectedOptions.length !== 3) {}
      const res = await this.$http.get(`categories/${this.selectedOptions[2]}/attributes?sel=many`)
      this.arrDyparams = res.data.data
      this.arrDyparams.forEach(item => {
        item.attr_vals = item.attr_vals === 0 ? [] : item.attr_vals.trim().split(',')
      })
    },
    async getGoodscate () {
      const res = await this.$http.get('categories?type=3')
      // console.log(res)
      this.options = res.data.data
    },
    handleClick () {

    },
    showEditDia () {},
    showDeleteBox () {},
    handleClose (row, tag) {
      row.attr_vals.splice(row.attr_vals.indexOf(tag), 1)
    },

    showInput () {
      this.inputVisible = true
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },

    handleInputConfirm (row) {
      let inputValue = this.inputValue
      if (inputValue) {
        row.attr_vals.push(inputValue)
      }
      this.inputVisible = false
      this.inputValue = ''
    }
  }
}
</script>

<style scoped>
.alert{
  margin: 10px auto;
}
</style>
