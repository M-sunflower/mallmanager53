<template>
<el-card>
  <my-bread level1="商品管理" level2="商品列表"></my-bread>
  <el-alert class="alert" title="添加商品信息" type="success" center show-icon></el-alert>
  <el-steps :active="1*active" finish-status="success" simple style="margin-top: 20px" class="steps">
    <el-step title="基本信息" ></el-step>
    <el-step title="商品参数" ></el-step>
    <el-step title="商品属性" ></el-step>
    <el-step title="商品图片" ></el-step>
    <el-step title="商品内容" ></el-step>
  </el-steps>
  <el-form label-position="top" label-width="80px" :model="form" style="height: 400px; overflow: auto">
    <el-tabs @tab-click="tabChange()" v-model="active" tab-position="left">
      <el-tab-pane name="1" label="基本信息">
        <el-form-item label="商品名称">
          <el-input v-model="form.goods_name"></el-input>
        </el-form-item>
        <el-form-item label="商品价格">
          <el-input v-model="form.goods_price"></el-input>
        </el-form-item>
        <el-form-item label="商品重量">
          <el-input v-model="form.goods_weight"></el-input>
        </el-form-item>
        <el-form-item label="商品数量">
          <el-input v-model="form.goods_number"></el-input>
        </el-form-item>
        <el-form-item label="商品分类">
          <el-cascader
            v-model="selectedOptions"
            :options="options"
            :props="defaultProps"
            @change="handleChange"
            clearable>
          </el-cascader>
        </el-form-item>
      </el-tab-pane>
      <el-tab-pane name="2" label="商品参数">
        <el-form-item :label="item1.attr_name" v-for="(item1, index) in arrDyparams" :key="index">
          <el-checkbox-group v-model="item1.attr_vals">
            <el-checkbox border :label="item2" v-for="(item2,index) in item1.attr_vals" :key="index"></el-checkbox>
          </el-checkbox-group>
        </el-form-item>
      </el-tab-pane>
      <el-tab-pane name="3" label="商品属性">
        <el-form-item :label="item.attr_name" v-for="(item, index) in arrStaticparams" :key="index">
          <el-input :value="item.attr_vals"></el-input>
        </el-form-item>
      </el-tab-pane>
      <el-tab-pane name="4" label="商品图片">
        <el-form-item>
          <el-upload
            :headers="headers"
            action="http://localhost:8888/api/private/v1/upload"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :on-success="handleSuccess"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>
      </el-tab-pane>
      <el-tab-pane name="5" label="商品内容">
        <el-form-item>
          <el-button @click="addGoods" type="primary" style="margin-bottom: 10px">点我-添加商品</el-button>
          <quill-editor v-model="form.goods_introduce"></quill-editor>
        </el-form-item>
      </el-tab-pane>
    </el-tabs>
  </el-form>
</el-card>
</template>

<script>
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

import { quillEditor } from 'vue-quill-editor'
export default {
  name: 'GoodsAdd',
  components: {
    quillEditor
  },
  data () {
    return {
      active: '1',
      form: {
        goods_name: '',
        goods_cat: '',
        goods_price: '',
        goods_number: '',
        goods_weight: '',
        goods_introduce: '',
        pics: [],
        attrs: []
      },
      // 级联选择器绑定的数据
      selectedOptions: [],
      options: [],
      defaultProps: {
        label: 'cat_name',
        children: 'children',
        value: 'cat_id',
        expandTrigger: 'hover'
      },
      arrDyparams: [],
      arrStaticparams: [],
      headers: {
        Authorization: localStorage.getItem('token')
      }
    }
  },
  created () {
    this.getGoodscate()
  },
  methods: {
    handleChange () {

    },
    async getGoodscate () {
      const res = await this.$http.get('categories?type=3')
      // console.log(res)
      this.options = res.data.data
    },
    async tabChange () {
      if (this.active === '2') {
        if (this.selectedOptions.length !== 3) {
          this.$message.warning('请先选择商品三级分类')
          return
        }
        const res = await this.$http.get(`categories/${this.selectedOptions[2]}/attributes?sel=many`)
        // console.log(res)
        this.arrDyparams = res.data.data
        this.arrDyparams.forEach(item => {
          item.attr_vals = item.attr_vals === 0 ? [] : item.attr_vals.trim().split(',')
        })
      } else if (this.active === '3') {
        if (this.selectedOptions.length !== 3) {
          this.$message.warning('请先选择商品三级分类')
          return
        }
        const res = await this.$http.get(`categories/${this.selectedOptions[2]}/attributes?sel=only`)
        console.log(res)
        this.arrStaticparams = res.data.data
      }
    },
    handlePreview (file) {
    },
    handleRemove (file) {
      let Index = this.form.pics.findIndex((item) => {
        return item.pic === file.response.data.tmp_path
      })
      this.form.pics.splice(Index, 1)
    },
    handleSuccess (file) {
      console.log(file)
      this.form.pics.push({
        pic: file.data.tmp_path
      })
    },
    async addGoods () {
      this.form.goods_cat = this.selectedOptions.join(',')
      let arr1 = this.arrDyparams.map(item => {
        return {attr_id: item.attr_id, attr_vals: item.attr_vals}
      })
      let arr2 = this.arrStaticparams.map(item => {
        return {attr_id: item.attr_id, attr_vals: item.attr_vals}
      })
      this.form.attrs = [...arr1, ...arr2]
      const res = await this.$http.post(`goods`, this.form)
      console.log(res)
      this.$router.push({name: 'goods'})
    }
  }
}
</script>

<style scoped>
.alert{
  margin-top: 20px;
}
  .steps{
    margin-bottom: 20px;;
  }
</style>
