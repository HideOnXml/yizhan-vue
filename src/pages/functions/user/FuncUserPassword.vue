<template>
  <div class="page">
    <div class="login-box">
      <el-form
        :model="ruleForm"
        ref="ruleForm"
        label-position="left"
        label-width="0px"
        :rules="rules"
        class="demo-ruleForm login-container"
        status-icon
        v-loading.lock="loading"
      >
        <h3 class="title">修改密码</h3>
        <el-form-item prop="oldPass">
          <el-input
            type="password"
            v-model="ruleForm.oldPass"
            auto-complete="off"
            placeholder="旧密码"
          ></el-input>
        </el-form-item>
        <el-form-item prop="newPass">
          <el-input
            type="password"
            v-model="ruleForm.newPass"
            auto-complete="off"
            placeholder="新密码"
          ></el-input>
        </el-form-item>
        <el-form-item prop="checkPass">
          <el-input
            type="password"
            v-model="ruleForm.checkPass"
            auto-complete="off"
            placeholder="重复密码"
          ></el-input>
        </el-form-item>
        <el-form-item style="width:100%;">
          <el-button
            type="primary"
            style="width:100%;"
            @click.native.prevent="handleSubmit"
            :loading="loading"
          >确认</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { requestEditPassword } from '@/api/user'
export default {
  name: 'FuncUserPassword',
  data() {
    let validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else {
        if (this.ruleForm.checkPass !== '') {
          this.$refs.ruleForm.validateField('checkPass')
        }
        callback()
      }
    }
    let validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'))
      } else if (value !== this.ruleForm.newPass) {
        callback(new Error('两次输入密码不一致！'))
      } else {
        callback()
      }
    }
    return {
      loading: false,
      ruleForm: {
        oldPass: '',
        newPass: '',
        checkPass: ''
      },
      rules: {
        oldPass: [
          { required: true, message: '请输入旧密码', trigger: 'blur' }
        ],
        newPass: [
          { validator: validatePass, trigger: 'blur' }
        ],
        checkPass: [
          { validator: validatePass2, trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    handleSubmit(ev) {
      this.$refs.ruleForm.validate((valid) => {
        this.loading = true
        if (valid) {
          const params = {
            oldPass: this.ruleForm.oldPass,
            newPass: this.ruleForm.newPass,
            checkPass: this.ruleForm.checkPass,
            tid: JSON.parse(sessionStorage.getItem('user-info')).id
          }
          requestEditPassword(params).then(result => {
            this.loading = false
            this.$message({
              message: result.message,
              type: 'success',
              center: true
            })
            this.$router.push('/admin/login')
          })
        } else {
          this.loading = false
          console.log('error submit!!')
          return false
        }
      })
    }
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
  width: 400px;
  background-color: #fff;
  margin: 0;
  padding: 2.25em;
  box-sizing: border-box;
  border: solid 1px #ddd;
  border-radius: 0.5em;
  font-family: "Source Sans Pro", sans-serif;
}

.login-box .svgContainer {
  position: relative;
  width: 200px;
  height: 200px;
  margin: 0 auto 1em;
  border-radius: 50%;
  pointer-events: none;
}
</style>
