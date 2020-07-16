<template>
<el-card>
  <my-bread level1="商品管理" level2="商品分类"></my-bread>
  <el-row>
    <el-col :span="24" class="searchBox">
      <el-button type="success">添加分类</el-button>
    </el-col>
  </el-row>
  <el-table :data="tableData" style="width: 100%">
    <el-table-column prop="date" label="日期" width="180"></el-table-column>
    <el-table-column prop="name" label="姓名" width="180"></el-table-column>
    <el-table-column prop="address" label="地址"></el-table-column>
  </el-table>
</el-card>
</template>

<script>
export default {
  name: 'goodscate',
  data () {
    return {
      tableData: []
    }
  }
}
</script>

<style scoped>
.searchBox{
  margin: 10px auto;
}
</style>
