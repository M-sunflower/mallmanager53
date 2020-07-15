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
      <el-tree
        :data="treeList"
        show-checkbox
        node-key="id"
        default-expand-all
        :default-checked-keys="arrcheck"
        :props="defaultProps"
        ref="tree">
      </el-tree>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleRight = false">取 消</el-button>
        <el-button type="primary" @click="setRoleRights">确 定</el-button>
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
      dialogFormVisibleRight: false,
      treeList: [],
      defaultProps: {
        children: 'children',
        label: 'authName'
      },
      arrcheck: [],
      currRoleId: -1
    }
  },
  created () {
    this.getTableData()
  },
  methods: {
    async getTableData () {
      const res = await this.$http.get('roles')
      this.rolesList = res.data.data
    },
    async deleteRight (role, rightId) {
      const res = await this.$http.delete('roles/' + role.id + '/rights/' + rightId)
      role.children = res.data.data
    },
    async showRightDia (role) {
      this.currRoleId = role.id
      const res = await this.$http.get('rights/tree')
      console.log(role)
      this.treeList = res.data.data
      let arrtem = []
      role.children.forEach(item1 => {
        item1.children.forEach(item2 => {
          item2.children.forEach(item3 => {
            arrtem.push(item3.id)
          })
        })
      })
      console.log(arrtem)
      this.arrcheck = arrtem
      this.dialogFormVisibleRight = true
    },
    async setRoleRights () {
      let arr1 = this.$refs.tree.getCheckedKeys()
      let arr2 = this.$refs.tree.getHalfCheckedKeys()
      let arr = [...arr1, ...arr2]
      const res = await this.$http.post('roles/' + this.currRoleId + '/rights', {rids: arr.join(',')})
      console.log(res)
      if (res.meta.status === 200) {
        this.getTableData()
      }
      this.dialogFormVisibleRight = false
    }
  }
}
</script>

<style scoped>
.addbtn{
  margin-top: 20px;
}
</style>
