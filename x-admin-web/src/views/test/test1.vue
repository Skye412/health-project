<template>
  <div class="upload-container">
    <el-card class="form-card" shadow="always">
      <div class="form-header">
        <div class="icon-box"><i class="el-icon-document-add"></i></div>
        <h1 class="title">身体信息录入</h1>
        <p class="subtitle">全面记录您的身体数据，以便 AI 为您提供更精准的健康评估</p>
      </div>

      <el-form
        :model="form"
        :rules="rules"
        ref="form"
        label-width="110px"
        class="modern-form"
        size="medium"
        label-position="right"
      >
        <div class="section-block">
          <h3 class="section-title"><i class="el-icon-user"></i> 基本资料</h3>
          <el-row :gutter="30">
            <el-col :xs="24" :sm="12">
              <el-form-item label="姓名" prop="name">
                <el-input v-model="form.name" placeholder="请输入姓名" prefix-icon="el-icon-user"></el-input>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="年龄" prop="age">
                <el-input-number v-model="form.age" :min="0" class="full-width" placeholder="岁"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="性别" prop="gender">
                <el-radio-group v-model="form.gender">
                  <el-radio-button label="男"><i class="el-icon-male"></i> 男</el-radio-button>
                  <el-radio-button label="女"><i class="el-icon-female"></i> 女</el-radio-button>
                </el-radio-group>
              </el-form-item>
            </el-col>
          </el-row>
        </div>

        <div class="section-block">
          <h3 class="section-title"><i class="el-icon-cpu"></i> 生理核心指标</h3>
          <el-row :gutter="30">
            <el-col :xs="24" :sm="12">
              <el-form-item label="身高 (m)" prop="height">
                <el-input-number v-model="form.height" :min="0" :step="0.01" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="体重 (kg)" prop="weight">
                <el-input-number v-model="form.weight" :min="0" :step="0.1" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="血糖 (mmol/L)" prop="bloodSugar">
                <el-input-number v-model="form.bloodSugar" :min="0" :step="0.1" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="血压 (mmHg)" prop="bloodPressure">
                <el-input-number v-model="form.bloodPressure" :min="0" :step="1" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="胆固醇 (mmol/L)" prop="bloodLipid">
                <el-input-number v-model="form.bloodLipid" :min="0" :step="0.1" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="心率 (BPM)" prop="heartRate">
                <el-input-number v-model="form.heartRate" :min="0" :step="1" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="视力 (度)" prop="vision">
                <el-input-number v-model="form.vision" :min="0" :step="25" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
          </el-row>
        </div>

        <div class="section-block">
          <h3 class="section-title"><i class="el-icon-coffee-cup"></i> 生活与作息习惯</h3>
          <el-row :gutter="30">
            <el-col :xs="24" :sm="12">
              <el-form-item label="睡眠时长 (h)" prop="sleepDuration">
                <el-input-number v-model="form.sleepDuration" :min="0" :step="0.5" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="睡眠质量" prop="sleepQuality">
                <el-radio-group v-model="form.sleepQuality" size="medium">
                  <el-radio label="好">好</el-radio>
                  <el-radio label="一般">一般</el-radio>
                  <el-radio label="差">差</el-radio>
                </el-radio-group>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="饮水量 (ml)" prop="waterConsumption">
                <el-input-number v-model="form.waterConsumption" :min="0" :step="100" class="full-width"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12">
              <el-form-item label="喜好食物" prop="foodTypes">
                <el-select v-model="form.foodTypes" placeholder="请选择最常摄入的食物" class="full-width">
                  <el-option label="蔬菜" value="蔬菜"></el-option>
                  <el-option label="水果" value="水果"></el-option>
                  <el-option label="肉类" value="肉类"></el-option>
                  <el-option label="鱼类" value="鱼类"></el-option>
                  <el-option label="豆类" value="豆类"></el-option>
                  <el-option label="谷物" value="谷物"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
          </el-row>
          
          <el-row :gutter="30" class="switch-row">
            <el-col :xs="8" :sm="8">
              <el-form-item label="是否吸烟" prop="smoking">
                <el-switch v-model="form.smoking" active-color="#F56C6C" inactive-color="#DCDFE6"></el-switch>
              </el-form-item>
            </el-col>
            <el-col :xs="8" :sm="8">
              <el-form-item label="是否饮酒" prop="drinking">
                <el-switch v-model="form.drinking" active-color="#E6A23C" inactive-color="#DCDFE6"></el-switch>
              </el-form-item>
            </el-col>
            <el-col :xs="8" :sm="8">
              <el-form-item label="是否运动" prop="exercise">
                <el-switch v-model="form.exercise" active-color="#67C23A" inactive-color="#DCDFE6"></el-switch>
              </el-form-item>
            </el-col>
          </el-row>
        </div>

        <el-divider></el-divider>

        <div class="form-actions">
          <el-button type="primary" @click="submitForm()" class="submit-btn" icon="el-icon-upload">保存并生成评估</el-button>
          <el-button @click="resetForm('form')" class="reset-btn" icon="el-icon-refresh-left" plain>重新填写</el-button>
        </div>
      </el-form>
    </el-card>
  </div>
</template>
  
<script>
import FunctionApi from "@/api/Function_Menu";
import AvatarUpload from "@/components/HeadImage/AvatarUpload";
import userApi from "@/api/userManage";

export default {
  name: "InfoUpload",
  components: {
    AvatarUpload,
  },
  data() {
    return {
      id: null,
      form: {
        id: null,
        name: "",
        age: null,
        gender: "",
        height: undefined,
        weight: undefined,
        bloodSugar: undefined,
        bloodPressure: undefined,
        bloodLipid: undefined,
        heartRate: undefined,
        vision: undefined,
        sleepDuration: undefined,
        sleepQuality: "",
        smoking: false,
        drinking: false,
        exercise: false,
        foodTypes: "",
        waterConsumption: undefined,
      },

      rules: {
        name: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          { min: 2, max: 10, message: "长度在 2 到 10 个字符", trigger: "blur" },
        ],
        age: [
          { required: true, message: "请输入年龄", trigger: "blur" },
          { type: "number", message: "年龄必须为数字值" },
        ],
        gender: [{ required: true, message: "请选择性别", trigger: "change" }],
        height: [
          { required: true, message: "请输入身高", trigger: "blur" },
          { type: "number", message: "身高必须为数字值" },
        ],
        weight: [
          { required: true, message: "请输入体重", trigger: "blur" },
          { type: "number", message: "体重必须为数字值" },
        ],
        bloodSugar: [
          { required: true, message: "请输入血糖", trigger: "blur" },
          { type: "number", message: "血糖必须为数字值" },
        ],
        bloodPressure: [
          { required: true, message: "请输入血压", trigger: "blur" },
          { type: "number", message: "血压必须为数字值" },
        ],
        bloodLipid: [
          { required: true, message: "请输入血脂", trigger: "blur" },
          { type: "number", message: "血脂必须为数字值" },
        ],
        heartRate: [
          { required: true, message: "请输入心率", trigger: "blur" },
          { type: "number", message: "心率必须为数字值" },
        ],
        vision: [
          { required: true, message: "请输入视力", trigger: "blur" },
          { type: "number", message: "视力必须为数字值" },
        ],
        sleepQuality: [
          { required: true, message: "请选择睡眠质量", trigger: "change" },
        ],
        foodTypes: [
          { required: true, message: "请选择摄入较多的食物种类", trigger: "change" },
        ],
        waterConsumption: [
          { required: true, message: "请输入饮水量", trigger: "blur" },
          { type: "number", message: "饮水量必须为数字值" },
        ],
      },
    };
  },

  methods: {
    submitForm() {
      // 如果表单数据中没有 id 属性，则将组件的 id 属性赋值给表单数据的 id 属性
      if (!this.form.id) {
        this.form.id = this.id;
      }

      this.$refs.form.validate((valid) => {
        if (valid) {
          FunctionApi.BodyInformation(this.form)
            .then((response) => {
              this.$message({
                type: "success",
                message: response.message || "上传成功！",
              });
            })
            .catch((error) => {});
            
          FunctionApi.BodyInformationNotes(this.form)
            .then((response) => {})
            .catch((error) => {});
        } else {
          return false;
        }
      });
    },

    // 重置表单数据的方法
    resetForm(formName) {
      this.$refs[formName].resetFields();
      this.$message({
        message: "表单已清空",
        type: "info",
      });
    },

    async getUserId() {
      await userApi.getUserId().then((response) => {
        // 如果请求成功并且返回的数据包含 id 属性，则将其转换为整数并赋值给组件的 id 属性
        if (response && response.data) {
          this.id = parseInt(response.data.id);
        }
      });
    },
  },

  created() {
    this.getUserId();
  },
};
</script>

<style lang="scss" scoped>
.upload-container {
  min-height: calc(100vh - 84px);
  background: linear-gradient(135deg, #f5f7fa 0%, #e4eaf5 100%);
  padding: 40px 20px;
  display: flex;
  justify-content: center;
  align-items: flex-start;

  .form-card {
    width: 100%;
    max-width: 900px;
    border-radius: 16px;
    border: none;
    box-shadow: 0 15px 35px rgba(0, 0, 0, 0.05);
    background-color: rgba(255, 255, 255, 0.95);
    backdrop-filter: blur(10px);
  }

  .form-header {
    text-align: center;
    margin-bottom: 40px;

    .icon-box {
      width: 64px;
      height: 64px;
      margin: 0 auto 15px;
      background: linear-gradient(135deg, #409EFF 0%, #66b1ff 100%);
      border-radius: 50%;
      display: flex;
      justify-content: center;
      align-items: center;
      box-shadow: 0 8px 16px rgba(64, 158, 255, 0.3);

      i { font-size: 30px; color: white; }
    }

    .title {
      font-size: 26px;
      color: #303133;
      font-weight: bold;
      margin: 0 0 10px;
    }

    .subtitle {
      font-size: 14px;
      color: #909399;
      margin: 0;
    }
  }

  .modern-form {
    padding: 0 20px;

    .section-block {
      margin-bottom: 35px;

      .section-title {
        font-size: 16px;
        color: #409EFF;
        margin-bottom: 20px;
        padding-bottom: 10px;
        border-bottom: 1px solid #ebeef5;
        font-weight: 600;
        display: flex;
        align-items: center;

        i { margin-right: 8px; font-size: 18px; }
      }
    }

    /* 强制所有数字输入框和选择框填满列宽 */
    ::v-deep .full-width {
      width: 100% !important;
      
      .el-input__inner { text-align: left; }
    }

    .switch-row {
      margin-top: 10px;
      ::v-deep .el-form-item__label { font-weight: normal; }
    }

    .form-actions {
      text-align: center;
      margin-top: 30px;
      margin-bottom: 10px;

      .submit-btn {
        padding: 12px 35px;
        font-size: 16px;
        border-radius: 25px;
        background: linear-gradient(90deg, #409EFF, #3a8ee6);
        border: none;
        box-shadow: 0 6px 15px rgba(64, 158, 255, 0.3);
        transition: transform 0.2s;

        &:hover { transform: translateY(-2px); box-shadow: 0 8px 20px rgba(64, 158, 255, 0.4); }
      }

      .reset-btn {
        padding: 12px 30px;
        font-size: 16px;
        border-radius: 25px;
        margin-left: 20px;
      }
    }
  }
}
</style>