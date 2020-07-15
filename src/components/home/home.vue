<template>
  <el-container class="container">
    <el-header class="header">
      <el-row>
        <el-col :span="4"><div class="grid-content bg-purple">
          <img src = '../../assets/logo.png' alt = '无法显示图片'/>
        </div></el-col>
        <el-col :span="18" class="middle"><h2>电商后台管理系统</h2></el-col>
        <el-col :span="2"><div class="grid-content bg-purple"><a class="logout" href="#" @click.prevent="handleSignout()">退出</a> </div></el-col>
      </el-row>
    </el-header>
    <el-container>
      <el-aside width="200px" class="aside">
        <el-menu :unique-opened= "true" :router="true">
          <el-submenu :index="'' + item1.order" v-for="(item1, index) in menus" :key="index">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>{{item1.authName}}</span>
            </template>
              <el-menu-item :index="item2.path" v-for="(item2, index) in item1.children" :key="index">
                <i class="el-icon-menu"></i>
                <span>{{item2.authName}}</span>
              </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main class="main">
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  created () {
    this.getMenus()
  },
  data () {
    return {
      menus: []
    }
  },
  methods: {
    handleSignout () {
      localStorage.clear()
      this.$message({
        showClose: true,
        message: '退出成功',
        type: 'success'
      })
      this.$router.push({name: 'login'})
    },
    async getMenus () {
      const res = await this.$http.get('menus')
      this.menus = res.data.data
    }
  }
}
</script>

<style scoped>
.container{
  height: 100%;
}
  .header{
    background-color: #b3c0d1;
  }
  .aside{
    background-color: #d3dce6;
  }
  .main{
    background-color: #e9eef3;
  }
  img{
    height: 60px;
    width: 200px;
    text-align: center;
    vertical-align: middle;
  }
  .middle{
    text-align: center;
    color:#ffffff;
  }
  .logout{
    text-decoration: none;
    line-height: 60px;
  }
</style>
