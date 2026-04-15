<template>
  <div class="login-container">
    <el-card class="login-form-layout">
      <el-form autoComplete="on"
               :model="loginForm"
               :rules="loginRules"
               ref="loginForm"
               label-position="left">
        <div style="text-align: center">
          <svg-icon icon-class="login-mall" style="width: 56px;height: 56px;color: #409EFF"></svg-icon>
        </div>
        <h2 class="login-title color-main">{{ isRegister ? '新用户注册' : '系统登录' }}</h2>

        <el-form-item prop="username">
          <el-input name="username"
                    type="text"
                    v-model="loginForm.username"
                    autoComplete="on"
                    placeholder="请输入用户名">
          <span slot="prefix">
            <svg-icon icon-class="user" class="color-main"></svg-icon>
          </span>
          </el-input>
        </el-form-item>

        <el-form-item prop="password">
          <el-input name="password"
                    :type="pwdType"
                    @keyup.enter.native="handleAction"
                    v-model="loginForm.password"
                    autoComplete="on"
                    placeholder="请输入密码">
          <span slot="prefix">
            <svg-icon icon-class="password" class="color-main"></svg-icon>
          </span>
            <span slot="suffix" @click="showPwd">
            <svg-icon icon-class="eye" class="color-main"></svg-icon>
          </span>
          </el-input>
        </el-form-item>

        <el-form-item style="margin-bottom: 20px; text-align: center">
          <el-button style="width: 100%" type="primary" :loading="loading" @click.native.prevent="handleAction">
            {{ isRegister ? '立 即 注 册' : '登 录' }}
          </el-button>
        </el-form-item>

        <div class="login-footer-links">
          <el-link type="primary" :underline="false" @click="isRegister = !isRegister">
            {{ isRegister ? '已有账号？返回登录' : '没有账号？立即注册' }}
          </el-link>
        </div>
      </el-form>
    </el-card>
  </div>
</template>

<script>
  import { isvalidUsername } from '@/utils/validate';
  import { setCookie, getCookie } from '@/utils/support';
  import { createAdmin } from '@/api/login'; // 对应接口定义文件中的 /admin/register

  export default {
    name: 'login',
    data() {
      const validateUsername = (rule, value, callback) => {
        if (!isvalidUsername(value)) {
          callback(new Error('请输入正确的用户名'))
        } else {
          callback()
        }
      };
      const validatePass = (rule, value, callback) => {
        if (value.length < 3) {
          callback(new Error('密码不能小于3位'))
        } else {
          callback()
        }
      };
      return {
        isRegister: false, // 核心状态控制
        loginForm: {
          username: '',
          password: '',
        },
        loginRules: {
          username: [{required: true, trigger: 'blur', validator: validateUsername}],
          password: [{required: true, trigger: 'blur', validator: validatePass}]
        },
        loading: false,
        pwdType: 'password'
      }
    },
    created() {
      // 这里的默认值可以根据你的开发习惯调整
      this.loginForm.username = getCookie("username") || 'admin';
      this.loginForm.password = getCookie("password") || '';
    },
    methods: {
      showPwd() {
        this.pwdType = this.pwdType === 'password' ? '' : 'password';
      },
      // 统一处理回车或点击事件
      handleAction() {
        if (this.isRegister) {
          this.handleRegister();
        } else {
          this.handleLogin();
        }
      },
      // 登录逻辑
      handleLogin() {
        this.$refs.loginForm.validate(valid => {
          if (valid) {
            this.loading = true;
            this.$store.dispatch('Login', this.loginForm).then(() => {
              this.loading = false;
              setCookie("username", this.loginForm.username, 15);
              setCookie("password", this.loginForm.password, 15);
              this.$router.push({path: '/'})
            }).catch(() => {
              this.loading = false
            })
          }
        })
      },
      // 注册逻辑：调用 api/login.js 中的 createAdmin (接口路径: /admin/register)
      handleRegister() {
        this.$refs.loginForm.validate(valid => {
          if (valid) {
            this.loading = true;
            const params = {
              username: this.loginForm.username,
              password: this.loginForm.password,
              nickName: '新用户',
              email: ''
            };
            createAdmin(params).then(res => {
              this.$message.success('注册成功，请使用新账号登录！');
              this.isRegister = false; // 注册完切回登录模式
              this.loading = false;
            }).catch(() => {
              this.loading = false;
            })
          }
        })
      }
    }
  }
</script>

<style scoped>
  /* 现代 Flex 居中布局，适配所有屏幕，不遮挡元素 */
  .login-container {
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    background: #f0f2f5; /* 纯净背景，去掉了带广告感的背景图 */
  }

  .login-form-layout {
    width: 380px;
    border-top: 5px solid #409EFF;
    box-shadow: 0 4px 15px rgba(0,0,0,0.08);
    background: #fff;
  }

  .login-title {
    text-align: center;
    margin-bottom: 30px;
    font-weight: 400;
  }

  .login-footer-links {
    text-align: center;
    margin-top: 10px;
  }
</style>
