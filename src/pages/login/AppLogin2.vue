<template>
  <div class="page">
    <div class="login-box">
      <el-form
        :model="ruleForm"
        :rules="rules"
        ref="ruleForm"
        label-position="left"
        label-width="0px"
        class="demo-ruleForm login-container"
        status-icon
      >
        <h3 class="title">系统登录</h3>
        <el-form-item prop="account">
          <el-input
            type="text"
            v-model="ruleForm.account"
            auto-complete="off"
            placeholder="账号"
            id="loginEmail"
          ></el-input>
        </el-form-item>
        <el-form-item prop="checkPass">
          <el-input
            type="password"
            v-model="ruleForm.checkPass"
            auto-complete="off"
            placeholder="密码"
            id="loginPassword"
          ></el-input>
          <label id="showPasswordToggle">
            <el-checkbox v-model="checked" id="showPasswordCheck">显示密码</el-checkbox>
          </label>
          <router-link to="/" style="float: right; color: #bbbbbb">忘记密码？</router-link>
        </el-form-item>
        <el-form-item style="width:100%;">
          <el-button
            type="primary"
            style="width:100%;"
            @click.native.prevent="handleSubmit"
            :loading="logining"
          >登录</el-button>
        </el-form-item>
        <el-form-item style="width:100%;">
          <router-link to="/register">
            <el-button style="width:100%;">注册</el-button>
          </router-link>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { requestLogin } from '@/api/user'
export default {
  name: 'app-login',
  data() {
    return {
      formUrl: '/',
      logining: false,
      ruleForm: {
        account: 'admin',
        checkPass: '123456'
      },
      rules: {
        account: [{
          required: true,
          message: '请输入账号',
          trigger: 'blur'
        }],
        checkPass: [{
          required: true,
          message: '请输入密码',
          trigger: 'blur'
        }]
      },
      checked: false
    }
  },
  methods: {
    handleSubmit(ev) {
      this.$refs.ruleForm.validate((valid) => {
        if (valid) {
          this.logining = true
          const loginParams = { username: this.ruleForm.account, password: this.ruleForm.checkPass }
          // 发起请求
          requestLogin(loginParams).then(data => {
            // 登录成功
            this.logining = false
            this.$message({
              message: '登录成功',
              type: 'success'
            })
            // 跳转到主页
            this.$router.push(this.formUrl)
          }).catch(err => {
            // 登录失败
            this.logining = false
            console.log(err)
          })
        } else {
          console.log('error submit!')
          return false
        }
      })
    }
  },
  beforeRouteEnter(to, from, next) {
    // 获取上一个页面的URL，以便登录后能跳转至该URL，而非固定的首页
    next(vm => {
      if (!from.meta.errorPage)
        vm.formUrl = from.fullPath
    })
  }
}
</script>

<style scoped>
.title {
  text-align: center;
  margin-bottom: 15px;
}

.page {
  background-color: #eff3f4;
  position: absolute;
  width: 100%;
  height: 100%;
  font-size: 16px;
  font-family: "Source Sans Pro", sans-serif;
  font-weight: 400;
  -webkit-font-smoothing: antialiased;
}

.login-box {
  position: absolute;
  top: 50%;
  left: 50%;
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
  display: block;
  width: 100%;
  max-width: 400px;
  background-color: #fff;
  margin: 0;
  padding: 2.25em;
  box-sizing: border-box;
  border: solid 1px #ddd;
  border-radius: 0.5em;
  font-family: "Source Sans Pro", sans-serif;
}
</style>
