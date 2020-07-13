<template>
  <el-card>
    <my-bread level1="权限管理" level2="角色列表"></my-bread>
    <el-button class="addbtn" type="info" plain>添加角色</el-button>
    <el-table :data="rolesList" style="width: 100%">
      <el-table-column type="expand">
        <template slot-scope="scope">
          <el-row v-for="(item1,index) in scope.row.children" :key="index">
            <el-col :span="4">
              <el-tag @close="deleteRight(scope.row,item1.id)" closable>{{item1.authName}}</el-tag>
              <i class="el-icon-arrow-right"></i>
            </el-col>
            <el-col :span="20">
              <el-row v-for="(item2,index) in item1.children" :key="index">
                <el-col :span="4">
                  <el-tag  @close="deleteRight(scope.row,item2.id)" closable type="success">{{item2.authName}}</el-tag>
                  <i class="el-icon-arrow-right"></i>
                </el-col>
                <el-col :span="20">
                  <el-tag @close="deleteRight(scope.row,item3.id)" v-for="(item3,index) in item2.children" :key="index" type="danger" closable>
                    {{item3.authName}}
                  </el-tag>
                </el-col>
              </el-row>
            </el-col>
          </el-row>
          <span v-if="scope.row.children.length===0">未分配权限</span>
        </template>
      </el-table-column>
      <el-table-column  type="index" label="#" prop="id" width="160px"></el-table-column>
      <el-table-column label="角色名称" prop="roleName" width="200px"></el-table-column>
      <el-table-column label="角色描述" prop="roleDesc" width="200px"></el-table-column>
      <el-table-column label="操作" prop="desc">
        <template slot-scope="scope">
          <el-button size="mini" plain type="primary" icon="el-icon-edit" circle></el-button>
          <el-button size="mini" plain type="danger" icon="el-icon-delete" circle></el-button>
          <el-button @click='showRightDia(scope.row)' size="mini" plain type="success" icon="el-icon-check" circle></el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog title="修改权限" :visible.sync="dialogFormVisibleRight">
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleRight = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisibleRight = false">确 定</el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  name: 'Role',
  data () {
    return {
      rolesList: [],
      dialogFormVisibleRight: false
    }
  },
  created () {
    this.getTableData()
  },
  methods: {
    async getTableData () {
      const res = await this.$http.get('roles')
      console.log(res)
      this.rolesList = res.data.data
    },
    async deleteRight (role, rightId) {
      const res = await this.$http.delete('roles/' + role.id + '/rights/' + rightId)
      console.log(res)
      role.children = res.data.data
    },
    showRightDia () {
      this.dialogFormVisibleRight = true
    }
  }
}
</script>

<style scoped>
.addbtn{
  margin-top: 20px;
}
</style>
