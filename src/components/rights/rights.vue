<template>
  <el-card>
    <my-bread level1="权限管理" level2="权限列表"></my-bread>
    <el-table :data="rightsList" border stripe style="width: 100%" height="500px">
      <el-table-column type="index" label="#" width="80"></el-table-column>
      <el-table-column prop="authName" label="权限名称" width="180"></el-table-column>
      <el-table-column prop="path" label="路径"></el-table-column>
      <el-table-column label="层级">
        <template slot-scope="scope">
          <span v-if="scope.row.level==0">一级</span>
          <span v-if="scope.row.level==1">二级</span>
          <span v-if="scope.row.level==2">三级</span>
        </template>
      </el-table-column>
    </el-table>
  </el-card>
</template>

<script>
export default {
  name: 'right',
  data () {
    return {
      rightsList: []
    }
  },
  created () {
    // eslint-disable-next-line no-undef
    this.getTableData()
  },
  methods: {
    async getTableData () {
      const res = await this.$http.get('rights/list')
      console.log(res)
      this.rightsList = res.data.data
    }
  }
}
</script>

<style scoped>

</style>
