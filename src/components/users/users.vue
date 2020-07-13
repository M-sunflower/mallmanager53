<template>
  <el-card class = 'box-card'>
    <!--面包屑-->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item>首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!--搜索框-->
    <el-row class="searchRow">
      <el-col>
        <el-input placeholder="请输入内容" v-model="queryInfo.query" class="inputSearch" clearable @clear = "resetList">
          <el-button @click = "searchUsers" slot="append" icon="el-icon-search"></el-button>
        </el-input>
        <el-button @click="showAddUserDia" type="success" plain>添加用户</el-button>
      </el-col>
    </el-row>
    <!--添加用户对话框-->
    <el-dialog title="添加用户" :visible.sync="dialogFormVisibleAdd">
      <el-form :model="form">
        <el-form-item label="用户名" label-width="100px">
          <el-input v-model="form.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密 码" label-width="100px">
          <el-input v-model="form.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" label-width="100px">
          <el-input v-model="form.email" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话" label-width="100px">
          <el-input v-model="form.mobile" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="Cancel">取 消</el-button>
        <el-button type="primary" @click="AddUser">确 定</el-button>
      </div>
    </el-dialog>
    <!--表格-->
    <el-table :data="userList" style="width: 100%">
      <el-table-column type="index" label="#" width="60">
      </el-table-column>
      <el-table-column prop="username" label="姓名" width="80"></el-table-column>
      <el-table-column prop="email" label="邮箱" width="180"></el-table-column>
      <el-table-column prop="mobile" label="电话" width="120"></el-table-column>
      <el-table-column label="创建时间" width="120">
        <template slot-scope="scope">{{scope.row.create_time|fmtDate}}</template>
      </el-table-column>
      <el-table-column label="用户状态">
        <template slot-scope="scope">
          <el-switch @change="ChangeMg_state(scope.row)" v-model="scope.row.mg_state" active-color="#13ce66" inactive-color="#ff4949">
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button @click="showEditDia(scope.row)" size="mini" plain type="primary" icon="el-icon-edit" circle></el-button>
          <el-button @click="showDeleteBox(scope.row.id)" size="mini" plain type="danger" icon="el-icon-delete" circle></el-button>
          <el-button @click="setUsersRole(scope.row)" size="mini" plain type="success" icon="el-icon-check" circle></el-button>
        </template>
      </el-table-column>
    </el-table>
    <!--编辑信息-->
    <el-dialog title="编辑用户信息" :visible.sync="dialogFormVisibleEdit">
    <el-form :model="form">
      <el-form-item label="用户名" label-width="100px">
        <el-input v-model="form.username" autocomplete="off" disabled></el-input>
      </el-form-item>
      <el-form-item label="邮箱" label-width="100px">
        <el-input v-model="form.email" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="电话" label-width="100px">
        <el-input v-model="form.mobile" autocomplete="off"></el-input>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="Cancel">取 消</el-button>
      <el-button type="primary" @click="ChangeUser">确 定</el-button>
    </div>
    </el-dialog>
    <!--分配角色-->
    <el-dialog title="角色分配" :visible.sync="dialogFormVisibleRole">
      <el-form :model="form">
        <el-form-item label="用户名" label-width="100px">
          {{form.username}}
        </el-form-item>
        <el-form-item label="角色" label-width="100px">
          <el-select v-model="currRoleId">
            <el-option label="请选择" value="-1"></el-option>
            <el-option :label="item.roleName" :value="item.id" v-for="(item, index) in roles" :key="index"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleRole = false">取 消</el-button>
        <el-button type="primary" @click="setRole">确 定</el-button>
      </div>
    </el-dialog>
    <!--分页-->
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pagenum"
      :page-sizes="[1, 2, 3, 4]"
      :page-size="2"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
  </el-card>
</template>

<script>
export default {
  name: 'Users',
  data () {
    return {
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      userList: [],
      total: -1,
      dialogFormVisibleAdd: false,
      dialogFormVisibleEdit: false,
      dialogFormVisibleRole: false,
      form: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      currRoleId: '-1',
      roles: []
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    async getUserList () {
      // eslint-disable-next-line camelcase
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
    },
    handleSizeChange (val) {
      console.log(`每页 ${val} 条`)
      this.queryInfo.pagesize = val
      this.queryInfo.pagenum = 1
      this.getUserList()
    },
    handleCurrentChange (val) {
      console.log(`当前页: ${val}`)
      this.queryInfo.pagenum = val
      this.getUserList()
    },
    searchUsers () {
      this.getUserList()
    },
    resetList () {
      this.getUserList()
    },
    showAddUserDia () {
      this.form = {}
      this.dialogFormVisibleAdd = true
    },
    async AddUser () {
      const res = await this.$http.post('users', this.form)
      console.log(res)
      const {status, msg} = res.data.meta
      if (status === 201) {
        this.$message({
          showClose: true,
          message: msg,
          type: 'success'
        })
        this.getUserList()
        this.form = {}
      } else {
        this.$message({
          showClose: true,
          message: msg,
          type: 'error'
        })
      }
      this.dialogFormVisibleAdd = false
    },
    Cancel () {
      this.form = {}
      this.dialogFormVisibleAdd = false
      this.dialogFormVisibleEdit = false
    },
    showDeleteBox (ID) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        const res = await this.$http.delete('users/' + ID)
        if (res.data.meta.status === 200) {
          this.pagenum = 1
          this.getUserList()
        }
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    showEditDia (user) {
      this.dialogFormVisibleEdit = true
      this.form = user
    },
    async ChangeUser () {
      const res = await this.$http.put('users' + this.form.id, this.form)
      console.log(res)
      const {status, msg} = res.data.meta
      if (status === 200) {
        this.$message({
          showClose: true,
          message: msg,
          type: 'success'
        })
        this.getUserList()
      } else {
        this.$message({
          showClose: true,
          message: msg,
          type: 'error'
        })
      }
      this.dialogFormVisibleEdit = false
    },
    async ChangeMg_state (user) {
      const res = await this.$http.put('users/' + user.id + '/state/' + user.mg_state)
      const {meta: {status, msg}} = res.data
      if (status === 200) {
        this.$message({
          showClose: true,
          message: msg,
          type: 'success'
        })
      }
    },
    async setUsersRole (user) {
      this.form = []
      this.form = user
      const res1 = await this.$http.get('roles')
      console.log(res1)
      this.roles = res1.data.data
      const res = await this.$http.get('users/' + user.id)
      console.log(res)
      this.currRoleId = res.data.data.rid
      this.dialogFormVisibleRole = true
    },
    async setRole () {
      const res = await this.$http.put('users/' + this.form.id + '/role', {rid: this.currRoleId})
      console.log(res)
      this.dialogFormVisibleRole = false
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
