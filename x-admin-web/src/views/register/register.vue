<template>
  <div class="register-container">
    <div class="color-shape shape-1"></div>
    <div class="color-shape shape-2"></div>
    <div class="color-shape shape-3"></div>

    <div class="register-form-wrapper glass-card">
      <div class="title-container">
        <div class="logo-box">
          <i class="el-icon-plus"></i>
        </div>
        <h3 class="title">创建新账号</h3>
        <p class="subtitle">开启您的健康管理之旅</p>
      </div>

      <el-form :model="form" ref="form" :rules="rules" class="form">
        <el-form-item prop="username">
          <el-input v-model="form.username" placeholder="请输入用户名" prefix-icon="el-icon-user"></el-input>
        </el-form-item>
        
        <el-form-item prop="password">
          <el-input v-model="form.password" type="password" placeholder="请输入密码 (至少6位)" prefix-icon="el-icon-lock" show-password></el-input>
        </el-form-item>
        
        <el-form-item prop="confirmPassword">
          <el-input v-model="form.confirmPassword" type="password" placeholder="请再次确认密码" prefix-icon="el-icon-lock" show-password></el-input>
        </el-form-item>

        <el-form-item class="action-item">
          <el-button type="primary" class="register-btn glow-btn" @click="submitForm" :loading="loading">立即注册</el-button>
        </el-form-item>
        
        <div class="login-link">
          已有账号？ 
          <el-link type="primary" :underline="false" @click="goLogin">返回登录</el-link>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script>
import userApi from "@/api/userManage";

export default {
  data() {
    return {
      loading: false,
      form: {
        username: '',
        password: '',
        confirmPassword: '',
        email: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, message: '密码长度不能少于6位', trigger: 'blur' }
        ],
        confirmPassword: [
          { required: true, message: '请确认密码', trigger: 'blur' },
          { validator: this.validateConfirmPassword, trigger: 'blur' }
        ],
      }
    }
  },
  methods: {
    submitForm() {
      this.$refs.form.validate(valid => {
        if (valid) {
          this.loading = true;
          const requestBody = {
            username: this.form.username,
            password: this.form.password,
            email: this.form.email
          };
          
          userApi.register(requestBody).then(response => {
            this.$message({
              message: response.message || "注册成功！",
              type: "success"
            });
            this.loading = false;
            this.$router.push('/login');
          }).catch(() => {
            this.loading = false;
          });
        } else {
          return false;
        }
      });
    },
    validateConfirmPassword(rule, value, callback) {
      if (value !== this.form.password) {
        callback(new Error('两次输入的密码不一致'));
      } else {
        callback();
      }
    },
    goLogin() {
      this.$router.push('/login');
    }
  }
}
</script>

<style lang="scss" scoped>
.register-container {
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
    width: 450px; height: 450px;
    background: rgba(103, 194, 58, 0.3);
    top: -50px; left: -100px;
  }
  .shape-2 {
    width: 400px; height: 400px;
    background: rgba(64, 158, 255, 0.4);
    bottom: -100px; right: -50px;
    animation-delay: -3s;
  }
  .shape-3 {
    width: 300px; height: 300px;
    background: rgba(142, 68, 173, 0.15);
    top: 30%; left: 70%;
    animation-duration: 12s;
  }

  @keyframes float {
    0% { transform: translate(0, 0) scale(1); }
    100% { transform: translate(40px, -30px) scale(1.1); }
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
      background: linear-gradient(135deg, #67C23A, #85ce61);
      border-radius: 16px;
      display: flex;
      justify-content: center;
      align-items: center;
      box-shadow: 0 4px 15px rgba(103, 194, 58, 0.3);
      
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

  ::v-deep .el-input__inner {
    height: 48px;
    line-height: 48px;
    border-radius: 12px;
    background-color: rgba(255, 255, 255, 0.7);
    border: 1px solid rgba(255, 255, 255, 0.5);
    transition: all 0.3s;
    
    &:focus {
      background-color: rgba(255, 255, 255, 0.9);
      border-color: #409EFF;
      box-shadow: 0 0 10px rgba(64, 158, 255, 0.1);
    }
  }

  ::v-deep .el-input__prefix {
    left: 10px;
    .el-input__icon {
      line-height: 48px;
      font-size: 18px;
      color: #909399;
    }
  }
  
  ::v-deep .el-form-item {
    margin-bottom: 24px;
  }

  .action-item {
    margin-top: 35px;
    margin-bottom: 20px;
  }

  .glow-btn {
    width: 100%;
    height: 50px;
    border-radius: 25px;
    font-size: 16px;
    font-weight: bold;
    letter-spacing: 4px;
    background: linear-gradient(90deg, #67C23A, #5daf34);
    border: none;
    box-shadow: 0 6px 20px rgba(103, 194, 58, 0.4);
    transition: all 0.3s ease;
    
    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 8px 25px rgba(103, 194, 58, 0.5);
    }
  }

  .login-link {
    text-align: center;
    font-size: 14px;
    color: #606266;
  }
}
</style>