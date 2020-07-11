<template>
  <div class="login-wrap">
    <el-form class="login-form" label-position="top" label-width="80px" :model="formdata">
      <h2>用户登录</h2>
      <el-form-item label="用户名">
        <el-input v-model="formdata.username"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input v-model="formdata.password"></el-input>
      </el-form-item>
      <el-button  @click.prevent = "handleLogin()" class="login-btn" type="primary">登录</el-button>
    </el-form>
  </div>
</template>

<script>
export default {
  name: 'login',
  data () {
    return {
      formdata: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    handleLogin () {
      this.$http.post('login', this.formdata).then((res) => {
        const {data, meta: {msg, status}} = res.data
        if (status === 200) {
          console.log(res)
          localStorage.setItem('token', data.token)
          this.$router.push({name:'home'})
          this.$message({
            showClose: true,
            message: msg,
            type: 'success'
          })
        } else {
          this.$message({
            showClose: true,
            message: msg,
            type: 'error'
          })
        }
      })
    }
  }
}
</script>

<style scoped>
.login-wrap{
  height: 100%;
  background-color: #324152;
  display: flex;
  justify-content: center;
  align-items: center;
}
  .login-wrap .login-form{
    width: 400px;
    background-color: #ffffff;
    border-radius: 5px;
    padding: 30px;
  }
  .login-wrap .login-btn{
    width:100%;
  }
</style>
