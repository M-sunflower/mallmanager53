<template>
    <el-card class="box-card">
      <my-bread level1="商品管理" level2="商品列表"></my-bread>
      <el-row>
        <el-col class="searchRow">
          <el-input placeholder="请输入内容" class="input-with-select" clearable>
            <el-button slot="append" icon="el-icon-search"></el-button>
          </el-input>
          <el-button type="success" plain @click="$router.push({name: 'goodsadd'})">添加商品</el-button>
        </el-col>
      </el-row>
      <!--表格-->
      <el-table :data="goodslist" border style="width: 100%" stripe>
        <el-table-column prop="date" label="日期" width="180"></el-table-column>
        <el-table-column prop="name" label="姓名" width="180"></el-table-column>
        <el-table-column prop="address" label="地址"></el-table-column>
      </el-table>
    </el-card>
</template>

<script>
export default {
  name: 'Goodslist',
  data () {
    return {
      goodslist: [],
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 10
      }
    }
  },
  created () {
    this.getGoodsList()
  },
  methods: {
    async getGoodsList () {
      const res = await this.$http.get('goods', {params: this.queryInfo})
      console.log(res)
    }
  }
}
</script>

<style scoped>
.searchRow{
  margin: 20px auto;
}
  .input-with-select{
    width: 300px;
  }
</style>
