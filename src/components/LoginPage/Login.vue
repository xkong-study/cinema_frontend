<template>
  <div id="poster">
      <el-form :model="loginForm" label-position="left" class="login"
        @keyup.enter.native="login"
      >
        <el-form-item label="username">
          <el-input size="small" type="text" v-model="loginForm.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="password">
          <el-input size="small" type="password" v-model="loginForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-button style="padding-right: 10px" type="text" @click="register">do not have account？</el-button>
        <el-checkbox size="medium" style="padding-left: 90px;color: white" v-model="checked">remember me</el-checkbox>
        <el-form-item style="padding: 10px 50px 0px 50px">
          <el-button  class="submit" size="medium" round type="primary" @click="login">submit</el-button>
        </el-form-item>
        <el-divider></el-divider>
        <el-form-item>
          <a class="login-way" href="/">
            <img src="../../assets/ico/wechat.png" style="object-fit: fill"/>
          </a>
          <a class="login-way" href="https://baidu.com">
            <img src="../../assets/ico/github.png" style="object-fit: fill"/>
          </a>
          <a class="login-way" href="https://baidu.com">
            <img src="../../assets/ico/weibo.png" style="object-fit: fill"/>
          </a>
        </el-form-item>
      </el-form>
    </div>
</template>

<script>
export default {
  name: "Login",
  data () {
    return {
      checked: false,
      loginForm: {
        username: '',
        password: ''
      },
      responseResult: []
    }
  },
  methods: {
    login () {
      const _this = this
      console.log(this.$store.state)
      const url = '/login?rememberMe=' + this.checked
      this.$axios
          .post(url, {
            username: this.loginForm.username,
            password: this.loginForm.password
          },)
          .then(successResponse => {
            if (successResponse.data.code === 200) {
              _this.$store.commit('login',_this.loginForm)
              const path = this.$route.query.redirect;
              this.$router.replace({path:path === '/movieset' || path === undefined ? '/' : path})
            }
            if (successResponse.data.code === 400) {
              alert(successResponse.data.message)
            }
          })
          .catch(failResponse => {
            alert(failResponse.data);
          })
    },
    register() {
      this.$router.replace('/register')
    }
  }
}
</script>

<style scoped>

  #poster {
    background: url("../../assets/images/login.jpg") no-repeat center;
    height: 100%;
    width: 100%;
    background-size: cover;
    position: fixed;
  }

  .login {
    margin:100px auto;
    width:300px;
    padding: 10px 20px 0px 20px;
    background: rgba(2, 10, 14, 0.5);
  }

  /deep/ .el-form-item__label {
    color: white;
  }

  .submit.el-button {
    width: 200px;
  }

  a.login-way {
    padding: 23px;
  }

</style>
