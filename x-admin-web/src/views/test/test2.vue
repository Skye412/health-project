<template>
  <div class="assessment-container">
    <el-row :gutter="24" class="top-section">
      <el-col :xs="24" :md="14">
        <el-card shadow="never" class="welcome-card gradient-blue">
          <div class="welcome-content">
            <h2 class="title">您的专属健康评估报告</h2>
            <p class="subtitle">
              系统已根据您上传的身体数据进行了多维度的自动评估。<br/>
              <span style="color: #ffeb3b; font-weight: bold;">注意：</span>以下风险分析仅供参考，并不能替代专业医疗诊断。
            </p>
            <div class="user-brief">
              <el-avatar icon="el-icon-user-solid" :size="50" class="user-avatar"></el-avatar>
              <div class="user-meta">
                <span class="user-name">受检者身体年龄：{{ bodyInfo.age || '--' }} 岁</span>
                <span class="user-tag">性别：{{ bodyInfo.gender || bodyInfo.sex || '未知' }}</span>
              </div>
            </div>
          </div>
          <div class="card-bg-icon"><i class="el-icon-data-analysis"></i></div>
        </el-card>
      </el-col>
      
      <el-col :xs="24" :md="10">
        <el-card shadow="never" class="score-card">
          <div class="score-header">综合健康评分</div>
          <div class="score-body">
            <el-progress 
              type="dashboard" 
              :percentage="score > 0 ? score : 0" 
              :color="scoreColor"
              :width="150"
              :stroke-width="12"
            >
              <template slot="default">
                <div class="score-text">
                  <span class="num">{{ score }}</span><span class="unit">分</span>
                </div>
                <div class="score-status" :style="{ color: scoreColor }">{{ scoreStatus }}</div>
              </template>
            </el-progress>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <h3 class="section-title"><i class="el-icon-monitor text-primary"></i> 身体核心指标</h3>
    <el-row :gutter="20" class="metrics-section">
      <el-col :span="6">
        <el-card shadow="hover" class="metric-card">
          <div class="metric-icon" style="background-color: #e6f2ff; color: #409EFF;">
            <i class="el-icon-user"></i>
          </div>
          <div class="metric-info">
            <div class="metric-label">身高 / 体重</div>
            <div class="metric-value">{{ bodyInfo.height || '--' }}<span class="unit">m</span> / {{ bodyInfo.weight || '--' }}<span class="unit">kg</span></div>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card shadow="hover" class="metric-card">
          <div class="metric-icon" style="background-color: #fdf6ec; color: #E6A23C;">
            <i class="el-icon-odometer"></i>
          </div>
          <div class="metric-info">
            <div class="metric-label">BMI 指数</div>
            <div class="metric-value">{{ bmiM || '--' }} <span class="unit"></span></div>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card shadow="hover" class="metric-card">
          <div class="metric-icon" style="background-color: #fef0f0; color: #F56C6C;">
            <i class="el-icon-cpu"></i>
          </div>
          <div class="metric-info">
            <div class="metric-label">血压 / 心率</div>
            <div class="metric-value">{{ bodyInfo.bloodPressure || '--' }} / {{ bodyInfo.heartRate || '--' }}<span class="unit">bpm</span></div>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card shadow="hover" class="metric-card">
          <div class="metric-icon" style="background-color: #f0f9eb; color: #67C23A;">
            <i class="el-icon-sugar"></i>
          </div>
          <div class="metric-info">
            <div class="metric-label">空腹血糖 / 胆固醇</div>
            <div class="metric-value">{{ bodyInfo.bloodSugar || '--' }} / {{ bodyInfo.bloodLipid || '--' }}</div>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <h3 class="section-title" style="margin-top: 30px;"><i class="el-icon-magic-stick text-purple"></i> 智能深度分析</h3>
    <el-row :gutter="24">
      <el-col :xs="24" :md="12">
        <el-card shadow="never" class="analysis-card">
          <div slot="header" class="card-header"><i class="el-icon-picture-outline"></i> 体型与肥胖分析</div>
          <div class="analysis-item">
            <span class="label">当前体型：</span>
            <el-tag :type="bodyType === '正常体型' ? 'success' : 'danger'" effect="dark">{{ bodyType || '--' }}</el-tag>
          </div>
          <div class="analysis-item">
            <span class="label">健康风险：</span>
            <span class="value">{{ determineHealthRisk || '无' }}</span>
          </div>
          <div class="analysis-item">
            <span class="label">潜在疾病：</span>
            <span class="value text-danger">{{ Disease_risk || '无明显风险' }}</span>
          </div>
          <div class="analysis-item">
            <span class="label">改善建议：</span>
            <span class="value">{{ bodyTypeSuggestion || '继续保持' }}</span>
          </div>
        </el-card>
      </el-col>

      <el-col :xs="24" :md="12">
        <el-card shadow="never" class="analysis-card">
          <div slot="header" class="card-header"><i class="el-icon-view"></i> 视力与习惯分析</div>
          <div class="analysis-item">
            <span class="label">近视等级：</span>
            <el-tag :type="visionType === '没有近视' ? 'success' : 'warning'" effect="dark">{{ visionType || '--' }}</el-tag>
            <span class="value text-muted" style="margin-left: 10px;">(左/右眼：{{ bodyInfo.vision }}度)</span>
          </div>
          <div class="analysis-item">
            <span class="label">护眼建议：</span>
            <span class="value">{{ visionSuggestion || '注意用眼卫生' }}</span>
          </div>
          <el-divider></el-divider>
          <div class="analysis-item">
            <span class="label">习惯画像：</span>
            <span class="value tag-list">{{ habits || '暂无数据' }}</span>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="24" style="margin-top: 24px; margin-bottom: 40px;">
      <el-col :span="24">
        <el-card shadow="never" class="energy-card">
          <div slot="header" class="card-header"><i class="el-icon-lightning"></i> 基础能量消耗状况 (BMR)</div>
          <div class="energy-content">
            <div class="energy-circle">
              <el-progress type="dashboard" :percentage="Number(Standard_hight) || 0" :color="'#409EFF'"></el-progress>
              <div class="energy-label">身体年龄达标率</div>
            </div>
            <div class="energy-circle">
              <el-progress type="dashboard" :percentage="Number(BMR) || 0" :color="'#F56C6C'"></el-progress>
              <div class="energy-label">基本能量消耗比</div>
            </div>
            <div class="energy-desc">
              <p>基础代谢率（BMR）是指人体在清醒而又极端安静的状态下，不受肌肉活动、环境温度、食物及精神紧张等影响时的能量代谢率。</p>
              <p v-if="risk" style="color: #F56C6C; margin-top: 10px;"><i class="el-icon-warning-outline"></i> 系统检测到您可能有以下风险因素：<b>{{ risk }}</b></p>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import userApi from "@/api/userManage";

export default {
  data() {
    return {
      bodyInfo: "",
      bmi: null,
      risk: "",
      Standard_hight: null,
      metabolic_rate: null,
      BMR: null,
      habits: "",
      score: 100,
      habits_count: [],
      // 控制仪表盘颜色
      scoreColor: '#67C23A',
      scoreStatus: '健康'
    };
  },

  computed: {
    bmiM() {
      if (!this.bodyInfo || !this.bodyInfo.weight) return 0;
      const weight = Number(this.bodyInfo.weight);
      const bmiValue = weight / (this.bodyInfo.height * this.bodyInfo.height);
      this.bmi = bmiValue.toFixed(2);
      return bmiValue.toFixed(2);
    },

    percentage() {
      return this.bmi ? Math.round((this.bmi / 35) * 100) : 0;
    },

    visionType() {
      if (!this.bodyInfo) return "";
      const vision = this.bodyInfo.vision;
      if (vision >= 600) return "高度近视";
      if (vision >= 300 && vision < 600) return "中度近视";
      if (vision > 0 && vision < 300) return "轻度近视";
      if (vision === 0) return "没有近视";
      return "未知";
    },

    visionSuggestion() {
      const visionType = this.visionType;
      if (visionType === "高度近视") return "积极治疗，建议就医";
      if (visionType === "中度近视") return "注意保护眼睛，定期检查视力";
      if (visionType === "轻度近视") return "加强锻炼，注意用眼卫生";
      if (visionType === "没有近视") return "很好，保持生活习惯，注意保护眼睛";
      return "";
    },

    bodyType() {
      if (!this.bmiM) return "";
      if (this.bmiM >= 28) return "肥胖型";
      if (this.bmiM > 24 && this.bmiM < 28) return "超重体型";
      if (this.bmiM >= 0 && this.bmiM <= 24) return "正常体型";
      return "";
    },

    determineHealthRisk() {
      if (this.bmiM >= 28) return "您的体重太大了，请马上减肥";
      if (this.bmiM > 24 && this.bmiM < 28) return "您的体重过大，请及时减肥";
      if (this.bmiM >= 0 && this.bmiM <= 24) return "您的体重正常，请保持健康生活";
      return "";
    },

    Disease_risk() {
      if (this.bmiM >= 28) return "心脏病、中风、高血压和高胆固醇，增加心脏病，还有糖尿病、呼吸系统疾病、关节炎等风险";
      if (this.bmiM > 24 && this.bmiM < 28) return "容易导致高血压、高胆固醇、心脏病、患糖尿病的风险";
      if (this.bmiM >= 0 && this.bmiM <= 24) return "风险不大，保证摄入足够的蛋白质、碳水和脂肪";
      return "";
    },

    bodyTypeSuggestion() {
      if (this.bodyType === "肥胖型") return "控制饮食，增加运动量，并寻求专业医师的指导。";
      if (this.bodyType === "超重体型") return "注意饮食健康，控制摄入量，并加强有氧运动。";
      if (this.bodyType === "正常体型") return "保持良好的生活习惯，均衡饮食，保持身体健康。";
      return "";
    },
  },

  methods: {
    async getBodyInfo() {
      try {
        const response = await userApi.getBodyInfo();
        if (response.data && response.data.bodyList && response.data.bodyList.length > 0) {
          this.bodyInfo = response.data.bodyList[0];
        }
      } catch (error) {
        console.log("获取身体信息错误");
      }
    },

    habits_customs() {
      if (!this.bodyInfo) return;
      let habits = [];
      this.habits_count = habits;
      
      if (this.bodyInfo.foodTypes === "蔬菜") habits.push("爱吃蔬菜");
      if (this.bodyInfo.foodTypes === "水果") habits.push("爱吃水果");
      if (this.bodyInfo.foodTypes === "肉类") habits.push("爱吃肉");
      if (this.bodyInfo.foodTypes === "鱼类") habits.push("爱吃鱼");
      if (this.bodyInfo.foodTypes === "豆类") habits.push("爱吃豆类");
      if (this.bodyInfo.foodTypes === "谷物") habits.push("爱吃五谷");

      habits.push(this.bodyInfo.bloodSugar > 7 ? "需控糖" : "血糖正常");
      habits.push(this.bodyInfo.bloodPressure > 130 ? "偏高压饮食" : "血压正常");
      habits.push(this.bodyInfo.heartRate > 100 ? "易焦虑紧张" : "心情不错");
      habits.push(this.bodyInfo.vision > 50 ? "过度劳累" : "作息规律");
      habits.push(this.bodyInfo.sleepDuration < 8 ? "睡眠不足" : "睡眠充足");
      habits.push(this.bodyInfo.smoking ? "吸烟" : "不吸烟");
      habits.push(this.bodyInfo.drinking ? "饮酒" : "不饮酒");
      habits.push(this.bodyInfo.exercise ? "积极锻炼" : "缺乏运动");
      habits.push(this.bodyInfo.waterConsumption < 1000 ? "饮水不足" : "饮水充足");

      this.habits = habits.join(" | ");
    },

    diseaserisk() {
      if (!this.bodyInfo) return;
      this.risk = "";
      if (this.bodyInfo.bloodPressure >= 140) this.risk += "高血压，";
      if (this.bodyInfo.bloodLipid > 3) this.risk += "高血脂，";
      if (this.bodyInfo.bloodSugar > 6.1) this.risk += "偏高血糖，";
      if (this.bodyInfo.exercise === false) this.risk += "缺乏运动，";
      if (this.bodyInfo.heartRate > 100) this.risk += "心动过速，";
      if (this.bodyInfo.sleepDuration < 7) this.risk += "睡眠不足，";
      if (this.bodyInfo.smoking) this.risk += "肺部隐患，";
      if (this.bodyInfo.vision > 300) this.risk += "近视，";
      
      // 去掉最后一个逗号
      this.risk = this.risk.replace(/，$/, '');
    },

    compareAgeAndHeight() {
      if (!this.bodyInfo) return;
      const height = this.bodyInfo.height * 100; // 如果原来是米，转为厘米估算
      const sex = this.bodyInfo.gender || this.bodyInfo.sex;
      let Standardhight = null;
      if (sex === "男") {
        Standardhight = (height - 80) * 0.7 + 160;
      } else {
        Standardhight = (height - 70) * 0.6 + 160;
      }
      this.Standard_hight = ((Standardhight / height) * 1).toFixed(2);
    },

    supersession() {
      if (!this.bodyInfo) return;
      const height = this.bodyInfo.height * 100;
      const weight = this.bodyInfo.weight;
      const age = this.bodyInfo.age || 25;
      const BMR1 = (88.36 + 13.4 * weight + 4.8 * height - 5.7 * age).toFixed(2);
      const reference = 1200; 
      this.BMR = Math.round((BMR1 / reference) * 100); 
    },

    scoreCom() {
      this.score = 100;
      if (this.bodyInfo.vision > 300) this.score -= 10;
      if (this.risk.split("，").length > 3) this.score -= 20;
      if (this.bmi > 24) this.score -= 15;
      if (this.Standard_hight < 80) this.score -= 10;
      
      if(this.score < 0) this.score = 0;

      // 动态改变颜色
      if (this.score >= 85) {
        this.scoreColor = '#67C23A';
        this.scoreStatus = '优秀';
      } else if (this.score >= 70) {
        this.scoreColor = '#E6A23C';
        this.scoreStatus = '良好';
      } else {
        this.scoreColor = '#F56C6C';
        this.scoreStatus = '亚健康';
      }
    },
  },

  watch: {
    bodyInfo: {
      deep: true,
      handler() {
        this.diseaserisk();
        this.compareAgeAndHeight();
        this.supersession();
        this.habits_customs();
        this.scoreCom();
      },
    },
  },

  created() {
    this.getBodyInfo();
  },
};
</script>

<style lang="scss" scoped>
.assessment-container {
  padding: 24px;
  background-color: #f5f7fa;
  min-height: calc(100vh - 84px);

  .text-primary { color: #409EFF; }
  .text-success { color: #67C23A; }
  .text-danger { color: #F56C6C; font-weight: bold; }
  .text-purple { color: #8e44ad; }
  .text-muted { color: #909399; font-size: 13px; }

  .section-title {
    font-size: 18px;
    color: #303133;
    margin: 20px 0 15px;
    font-weight: 600;
    
    i { margin-right: 5px; font-size: 20px; vertical-align: -2px; }
  }

  /* 顶部横幅样式 */
  .top-section {
    margin-bottom: 25px;

    .welcome-card {
      border-radius: 16px;
      border: none;
      height: 220px;
      position: relative;
      overflow: hidden;

      &.gradient-blue {
        background: linear-gradient(135deg, #409EFF 0%, #53a8ff 100%);
        color: white;
      }

      .welcome-content {
        position: relative;
        z-index: 2;
        padding: 10px;

        .title { margin: 0 0 10px; font-size: 24px; letter-spacing: 1px; }
        .subtitle { font-size: 14px; line-height: 1.6; opacity: 0.9; margin-bottom: 30px; max-width: 85%; }

        .user-brief {
          display: flex;
          align-items: center;

          .user-avatar {
            background-color: rgba(255,255,255,0.2);
            color: white;
            border: 2px solid rgba(255,255,255,0.4);
          }

          .user-meta {
            margin-left: 15px;
            display: flex;
            flex-direction: column;

            .user-name { font-weight: bold; font-size: 16px; margin-bottom: 4px; }
            .user-tag { font-size: 12px; opacity: 0.8; }
          }
        }
      }

      .card-bg-icon {
        position: absolute;
        right: -20px;
        bottom: -30px;
        font-size: 180px;
        opacity: 0.1;
        z-index: 1;
        transform: rotate(-15deg);
      }
    }

    .score-card {
      border-radius: 16px;
      border: none;
      height: 220px;
      box-shadow: 0 8px 24px rgba(0,0,0,0.04);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;

      .score-header { font-size: 16px; font-weight: bold; color: #606266; margin-bottom: 10px; width: 100%; text-align: center; }
      .score-text {
        .num { font-size: 36px; font-weight: bold; color: #303133; }
        .unit { font-size: 16px; color: #909399; margin-left: 2px; }
      }
      .score-status { font-size: 15px; font-weight: bold; margin-top: 5px; }
    }
  }

  /* 身体指标四宫格 */
  .metrics-section {
    .metric-card {
      border-radius: 12px;
      border: none;
      display: flex;
      align-items: center;
      padding: 15px;
      transition: transform 0.3s;

      &:hover { transform: translateY(-5px); box-shadow: 0 10px 20px rgba(0,0,0,0.06); }

      ::v-deep .el-card__body { display: flex; width: 100%; padding: 0; align-items: center; }

      .metric-icon {
        width: 54px; height: 54px; border-radius: 14px;
        display: flex; justify-content: center; align-items: center;
        font-size: 26px; margin-right: 15px;
      }

      .metric-info {
        flex: 1;
        .metric-label { font-size: 13px; color: #909399; margin-bottom: 5px; }
        .metric-value { font-size: 20px; font-weight: bold; color: #303133;
          .unit { font-size: 12px; font-weight: normal; color: #C0C4CC; margin-left: 2px; }
        }
      }
    }
  }

  /* 智能分析板块 */
  .analysis-card {
    border-radius: 12px;
    border: none;
    height: 100%;
    box-shadow: 0 4px 12px rgba(0,0,0,0.03);

    .card-header { font-weight: bold; font-size: 16px; color: #303133; i { margin-right: 5px;} }

    .analysis-item {
      margin-bottom: 16px;
      line-height: 1.5;
      display: flex;
      align-items: flex-start;
      
      .label { color: #909399; width: 85px; flex-shrink: 0;}
      .value { color: #303133; flex: 1;}
      .tag-list { color: #409EFF; font-weight: 500; line-height: 1.8;}
    }
  }

  /* 能量环形图板块 */
  .energy-card {
    border-radius: 12px;
    border: none;
    box-shadow: 0 4px 12px rgba(0,0,0,0.03);

    .card-header { font-weight: bold; font-size: 16px; color: #303133; }

    .energy-content {
      display: flex;
      align-items: center;
      padding: 10px 0;

      .energy-circle {
        text-align: center;
        margin-right: 40px;
        
        .energy-label { margin-top: 10px; color: #606266; font-size: 14px; font-weight: 500;}
      }

      .energy-desc {
        flex: 1;
        padding-left: 30px;
        border-left: 1px solid #ebeef5;
        color: #606266;
        line-height: 1.8;
      }
    }
  }
}
</style>