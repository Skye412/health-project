<template>
  <div class="dashboard-container">
    <el-row :gutter="20" class="stat-row">
      <el-col :span="6">
        <el-card shadow="hover" class="stat-card">
          <div class="card-body">
            <div class="card-info">
              <div class="card-label">身高</div>
              <div class="card-value">
                {{ bodyInfo.height || '--' }}<span class="card-unit">m</span>
              </div>
            </div>
            <div class="card-icon-wrapper" style="background-color: #e6f7ff;">
              <i class="el-icon-user-solid" style="color: #409EFF;"></i>
            </div>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card shadow="hover" class="stat-card">
          <div class="card-body">
            <div class="card-info">
              <div class="card-label">体重</div>
              <div class="card-value">
                {{ bodyInfo.weight || '--' }}<span class="card-unit">kg</span>
              </div>
            </div>
            <div class="card-icon-wrapper" style="background-color: #f6ffed;">
              <i class="el-icon-odometer" style="color: #67C23A;"></i>
            </div>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card shadow="hover" class="stat-card">
          <div class="card-body">
            <div class="card-info">
              <div class="card-label">BMI 指数</div>
              <div class="card-value">{{ bmi || '--' }}</div>
            </div>
            <div class="card-icon-wrapper" style="background-color: #fffb8f;">
              <i class="el-icon-date" style="color: #E6A23C;"></i>
            </div>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card shadow="hover" class="stat-card">
          <div class="card-body">
            <div class="card-info">
              <div class="card-label">年龄</div>
              <div class="card-value">
                {{ bodyInfo.age || '--' }}<span class="card-unit">岁</span>
              </div>
            </div>
            <div class="card-icon-wrapper" style="background-color: #fff1f0;">
              <i class="el-icon-s-data" style="color: #F56C6C;"></i>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="chart-section">
      <el-col :span="8">
        <el-card shadow="never">
          <div ref="myChart" style="height: 380px; width: 100%"></div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card shadow="never">
          <div id="chart-container" style="height: 380px; width: 100%"></div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card shadow="never">
          <div id="chart-containerLine" style="height: 380px; width: 100%"></div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import * as echarts from "echarts";
import userApi from "@/api/userManage";
import FunctionApi from "@/api/Function_Menu";

export default {
  data() {
    return {
      bodyInfo: {},
      bmi: null,
      BodyNotesInfo: [],

      vision: [],
      waterConsumption: [],
      bloodSugar: [],
      bloodPressure: [],
      date: [],
      heartRate: [],
    };
  },
  methods: {
    // 原汁原味的数据请求
    async getBodyInfo() {
      try {
        const { data: { bodyList: [bodyInfo] } } = await userApi.getBodyInfo();
        this.bodyInfo = bodyInfo || {};
      } catch (error) {
        console.error("获取身体信息错误", error);
      }
    },

    async getBodyNotes() {
      try {
        if (!this.bodyInfo.id) return;
        const response = await FunctionApi.getBodyNotes(this.bodyInfo.id);
        this.BodyNotesInfo = response.data || [];

        // 清空数组防止重复追加
        this.vision = [];
        this.waterConsumption = [];
        this.bloodSugar = [];
        this.bloodPressure = [];
        this.heartRate = [];
        this.date = [];

        this.BodyNotesInfo.forEach((note) => {
          this.vision.push(note.vision);
          this.waterConsumption.push(note.waterConsumption);
          this.bloodSugar.push(note.bloodSugar);
          this.bloodPressure.push(note.bloodPressure);
          this.heartRate.push(note.heartRate);
          const formattedDate = new Date(note.date).toLocaleString("en-US", {
            year: "numeric",
            month: "2-digit",
            day: "2-digit",
          });
          this.date.push(formattedDate);
        });
      } catch (error) {
        console.error("获取身体趋势信息错误", error);
      }
    },

    bmiM() {
      if (this.bodyInfo.weight && this.bodyInfo.height) {
        const weight = Number(this.bodyInfo.weight);
        const bmiValue = weight / (this.bodyInfo.height * this.bodyInfo.height);
        this.bmi = bmiValue.toFixed(2);
      }
    },

    // 视力柱状图
    BarChart() {
      const chartDom = document.getElementById("chart-container");
      if (!chartDom) return;
      const myChart = echarts.init(chartDom);
      const option = {
        color: ["#3398DB"],
        tooltip: { trigger: "axis", axisPointer: { type: "shadow" } },
        grid: { left: "3%", right: "4%", bottom: "3%", containLabel: true },
        title: { text: "视力变化趋势图", textStyle: { fontWeight: "normal", fontSize: 20, color: "#333" }, left: "center", top: 10 },
        xAxis: [{ type: "category", data: this.date, axisTick: { alignWithLabel: true }, axisLabel: { interval: 1, rotate: 45 } }],
        yAxis: [{ type: "value" }],
        series: [{
          name: "视力", type: "bar", barWidth: "50%", data: this.vision,
          itemStyle: { barBorderRadius: 4 }
        }],
      };
      myChart.setOption(option);
    },

    // 血压血糖折线图
    area() {
      const chartDom = document.getElementById("chart-containerLine");
      if (!chartDom) return;
      const myChart = echarts.init(chartDom);
      const option = {
        title: { text: "血压血糖变化", textStyle: { fontWeight: "normal", fontSize: 20, color: "#333" }, left: "center", top: 10 },
        tooltip: { trigger: "axis" },
        legend: { data: ["血压", "血糖"], top: 40 },
        grid: { left: "5%", right: "5%", bottom: "5%", containLabel: true },
        xAxis: { type: "category", data: this.date, axisLabel: { interval: 1 } },
        yAxis: { type: "value" },
        series: [
          { name: "血压", data: this.bloodPressure, type: "line", smooth: true },
          { name: "血糖", data: this.bloodSugar, type: "line", smooth: true },
        ],
      };
      myChart.setOption(option);
    },

    // 心率趋势图 (从你的 Watcher 里提出来的)
    heartRateChart() {
      const chartDom = this.$refs.myChart;
      if (!chartDom) return;
      const myChart = echarts.init(chartDom);
      const option = {
        title: { text: "心率变化趋势图", textStyle: { fontWeight: "normal", fontSize: 20, color: "#333" }, left: "center", top: 10 },
        tooltip: { trigger: "axis", formatter: function(params) { return params[0].name + "：" + params[0].value; } },
        grid: { left: "5%", right: "5%", bottom: "5%", containLabel: true },
        xAxis: { type: "category", data: this.date, axisLabel: { interval: 2 }, axisTick: { show: false } },
        yAxis: { type: "value", axisLine: { show: false }, splitLine: { lineStyle: { type: "dashed", color: "#eee" } }, axisTick: { show: false } },
        series: [{
          data: this.heartRate, type: "line", smooth: true,
          lineStyle: { width: 3, color: "#00bfff" },
          symbol: "circle", symbolSize: 8,
          itemStyle: { color: "#00bfff", borderColor: "#fff", borderWidth: 2 },
          markLine: { data: [{ type: "average", name: "平均值" }], lineStyle: { type: "dashed", color: "green", width: 2 }, symbol: "none" }
        }],
      };
      myChart.setOption(option);
    }
  },

  // 原汁原味的 Watcher，保证数据获取顺序
  watch: {
    bodyInfo: {
      deep: true,
      async handler() {
        this.bmiM(); 
        await this.getBodyNotes(); 
        // 确保 DOM 渲染完后再初始化图表
        this.$nextTick(() => {
          this.BarChart();
          this.area();
          this.heartRateChart();
        });
      },
    },
  },

  created() {
    this.getBodyInfo();
  }
};
</script>

<style lang="scss" scoped>
.dashboard-container {
  padding: 24px;
  background-color: #f0f2f5; 
  min-height: calc(100vh - 84px);

  /* 顶部 4 个小卡片样式 */
  .stat-row {
    margin-bottom: 24px;
    
    .stat-card {
      border-radius: 12px;
      border: none;
      transition: all 0.3s ease;
      &:hover {
        transform: translateY(-5px);
        box-shadow: 0 12px 24px rgba(0, 0, 0, 0.08);
      }
    }

    .card-body {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .card-label {
      font-size: 14px;
      color: #909399;
      margin-bottom: 10px;
    }
    .card-value {
      font-size: 28px;
      font-weight: bold;
      color: #303133;
    }
    .card-unit {
      font-size: 14px;
      font-weight: normal;
      margin-left: 4px;
      color: #606266;
    }
    .card-icon-wrapper {
      width: 56px; height: 56px;
      border-radius: 14px;
      display: flex;
      justify-content: center;
      align-items: center;
      i { font-size: 28px; }
    }
  }

  /* 下方 3 个图表区域样式 */
  .chart-section {
    ::v-deep .el-card {
      border-radius: 12px;
      border: none;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    }
  }
}
</style>