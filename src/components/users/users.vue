<template>
  <el-card class = 'box-card'>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item>首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-row class="searchRow">
      <el-col>
        <el-input placeholder="请输入内容" v-model="query" class="inputSearch">
          <el-button slot="append" icon="el-icon-search"></el-button>
        </el-input>
        <el-button type="success">添加用户</el-button>
      </el-col>
    </el-row>
    <el-table
      :data="userList"
      style="width: 100%">
      <el-table-column
        type="index"
        label="#"
        width="60">
      </el-table-column>
      <el-table-column
        prop="username"
        label="姓名"
        width="80">
      </el-table-column>
      <el-table-column
        prop="email"
        label="邮箱"
        width="180">
      </el-table-column>
      <el-table-column
        prop="mobile"
        label="电话"
        width="120">
      </el-table-column>
      <el-table-column
        label="创建时间"
        width="120">
        <template slot-scope="scope">{{scope.row.create_time|fmtDate}}</template>
      </el-table-column>
      <el-table-column
        prop="mg_state"
        label="用户状态">
      </el-table-column>
      <el-table-column
        label="操作">
      </el-table-column>
    </el-table>
  </el-card>
</template>

<script>
export default {
  name: 'Users',
  data () {
    return {
      query: '',
      queryInfo: {
        pagenum: 1,
        pagesize: 3
      },
      userList: [],
      total: ''
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    async getUserList () {
      // eslint-disable-next-line camelcase
      this.$http.defaults.headers.common['Authorization'] = localStorage.getItem('token')
      const res = await this.$http.get(
        'users', {params: this.queryInfo})
      console.log(res)
      const {meta: {status, msg}, data: {users, total}} = res.data
      if (status === 200) {
        this.userList = users
        this.total = total
        this.$message({
          showClose: true,
          message: msg,
          type: 'success'
        })
      } else {
        this.$message({
          showClose: true,
          message: msg,
          type: 'warning'
        })
      }
    }
  }
}
</script>

<style scoped>
.box-card{
  height:100%;
}
.searchRow{
  margin-top: 20px;
}
  .inputSearch{
    width: 300px;
  }
</style>
