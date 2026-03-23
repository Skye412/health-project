<template>
  <div class="login-container">
    <div class="color-shape shape-1"></div>
    <div class="color-shape shape-2"></div>
    <div class="color-shape shape-3"></div>

    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      class="login-form glass-card"
      auto-complete="on"
      label-position="left"
    >
      <div class="title-container">
        <div class="logo-box">
          <i class="el-icon-first-aid-kit"></i>
        </div>
        <h3 class="title">健康管理系统</h3>
        <p class="subtitle">Welcome Back</p>
      </div>

      <el-form-item prop="username">
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          ref="username"
          v-model="loginForm.username"
          placeholder="请输入用户名"
          name="username"
          type="text"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <el-form-item prop="password">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input
          :key="passwordType"
          ref="password"
          v-model="loginForm.password"
          :type="passwordType"
          placeholder="请输入密码"
          name="password"
          tabindex="2"
          auto-complete="on"
          @keyup.enter.native="handleLogin"
        />
        <span class="show-pwd" @click="showPwd">
          <svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" />
        </span>
      </el-form-item>

      <div class="action-container">
        <el-button
          :loading="loading"
          type="primary"
          class="login-btn glow-btn"
          @click.native.prevent="handleLogin"
        >
          登 录
        </el-button>
        <div class="register-link">
          还没有账号？ 
          <el-link type="primary" :underline="false" @click="handleRegister">立即注册</el-link>
        </div>
      </div>
    </el-form>
  </div>
</template>

<script>
import { validUsername } from "@/utils/validate";

export default {
  name: "Login",
  data() {
    const validateUsername = (rule, value, callback) => {
      if (!validUsername(value)) {
        callback(new Error("请输入正确的用户名"));
      } else {
        callback();
      }
    };
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error("输入的密码不能少于6位"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        username: "",
        password: "",
      },
      loginRules: {
        username: [{ required: true, trigger: "blur", validator: validateUsername }],
        password: [{ required: true, trigger: "blur", validator: validatePassword }],
      },
      loading: false,
      passwordType: "password",
      redirect: undefined,
    };
  },
  watch: {
    $route: {
      handler: function (route) {
        this.redirect = route.query && route.query.redirect;
      },
      immediate: true,
    },
  },
  methods: {
    showPwd() {
      if (this.passwordType === "password") {
        this.passwordType = "";
      } else {
        this.passwordType = "password";
      }
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    handleLogin() {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.loading = true;
          this.$store
            .dispatch("user/login", this.loginForm)
            .then(() => {
              this.$router.push({ path: this.redirect || "/" });
              this.loading = false;
            })
            .catch(() => {
              this.loading = false;
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    handleRegister() {
      this.$router.push({ path: "/register" });
    },
  },
};
</script>

<style lang="scss" scoped>
.login-container {
  min-height: 100vh;
  width: 100%;
  background-color: #eef2f5;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  overflow: hidden;

  /* 背景装饰光斑 */
  .color-shape {
    position: absolute;
    filter: blur(90px);
    border-radius: 50%;
    z-index: 0;
    animation: float 10s ease-in-out infinite alternate;
  }
  .shape-1 {
    width: 500px; height: 500px;
    background: rgba(64, 158, 255, 0.4);
    top: -100px; left: -100px;
  }
  .shape-2 {
    width: 400px; height: 400px;
    background: rgba(103, 194, 58, 0.3);
    bottom: -50px; right: -50px;
    animation-delay: -5s;
  }
  .shape-3 {
    width: 300px; height: 300px;
    background: rgba(230, 162, 60, 0.2);
    top: 40%; left: 60%;
    animation-duration: 15s;
  }

  @keyframes float {
    0% { transform: translate(0, 0) scale(1); }
    100% { transform: translate(30px, 50px) scale(1.1); }
  }

  /* 毛玻璃表单卡片 */
  .glass-card {
    position: relative;
    z-index: 1;
    width: 420px;
    max-width: 90%;
    padding: 45px 40px;
    background: rgba(255, 255, 255, 0.6);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border: 1px solid rgba(255, 255, 255, 0.8);
    border-radius: 20px;
    box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.1);
    animation: slideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1);
  }

  @keyframes slideUp {
    from { opacity: 0; transform: translateY(40px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .title-container {
    text-align: center;
    margin-bottom: 35px;

    .logo-box {
      width: 60px; height: 60px;
      margin: 0 auto 15px;
      background: linear-gradient(135deg, #409EFF, #53a8ff);
      border-radius: 16px;
      display: flex;
      justify-content: center;
      align-items: center;
      box-shadow: 0 4px 15px rgba(64, 158, 255, 0.3);
      
      i { font-size: 32px; color: #fff; }
    }

    .title {
      font-size: 24px;
      color: #303133;
      margin: 0;
      font-weight: bold;
      letter-spacing: 1px;
    }
    .subtitle {
      font-size: 14px;
      color: #909399;
      margin-top: 8px;
    }
  }

  .svg-container {
    padding: 0 10px;
    color: #909399;
    vertical-align: middle;
    width: 35px;
    display: inline-block;
  }

  .show-pwd {
    position: absolute;
    right: 15px;
    top: 50%;
    transform: translateY(-50%);
    font-size: 16px;
    color: #909399;
    cursor: pointer;
    transition: color 0.3s;
    &:hover { color: #409EFF; }
  }

  ::v-deep .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.5);
    background: rgba(255, 255, 255, 0.7);
    border-radius: 12px;
    margin-bottom: 24px;
    transition: all 0.3s ease;
    
    &:hover, &:focus-within {
      background: rgba(255, 255, 255, 0.9);
      border-color: #409EFF;
      box-shadow: 0 0 10px rgba(64, 158, 255, 0.1);
    }
    &.is-error { border-color: #F56C6C; }
  }

  ::v-deep .el-input {
    display: inline-block;
    height: 48px;
    width: 85%;

    input {
      background: transparent;
      border: 0px;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: #303133;
      height: 48px;
      font-size: 15px;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px transparent inset !important;
        transition: background-color 5000s ease-in-out 0s;
      }
    }
  }

  .action-container {
    margin-top: 35px;
    
    .glow-btn {
      width: 100%;
      height: 50px;
      border-radius: 25px;
      font-size: 16px;
      font-weight: bold;
      letter-spacing: 4px;
      background: linear-gradient(90deg, #409EFF, #3a8ee6);
      border: none;
      box-shadow: 0 6px 20px rgba(64, 158, 255, 0.4);
      transition: all 0.3s ease;
      
      &:hover {
        transform: translateY(-2px);
        box-shadow: 0 8px 25px rgba(64, 158, 255, 0.5);
      }
    }

    .register-link {
      text-align: center;
      margin-top: 25px;
      font-size: 14px;
      color: #606266;
    }
  }
}
</style>