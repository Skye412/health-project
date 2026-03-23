<template>
  <div class="app-container">
    <div class="search-header">
      <h2 class="page-title">探索健康运动知识</h2>
      <p class="page-subtitle">找到最适合你的运动方式与节奏</p>
      
      <div class="search-box-wrapper">
        <el-input
          placeholder="搜索运动种类，例如：游泳、慢跑..."
          v-model="searchText"
          class="main-search-input"
          @keyup.enter.native="Search"
          clearable
          @clear="showSearch"
        >
          <el-button slot="append" icon="el-icon-search" @click="Search" class="search-btn">搜索</el-button>
        </el-input>
      </div>
    </div>

    <div class="card-grid">
      <el-row :gutter="24">
        <el-col :xs="24" :sm="12" :md="8" :lg="8" :xl="6" v-for="(sportInfo, index) in sportInfos" :key="index">
          <el-card class="sport-card" shadow="hover">
            <div class="card-deco-line" :style="{ backgroundColor: getThemeColor(index) }"></div>
            
            <div class="card-content">
              <div class="card-header">
                <div class="icon-circle" :style="{ color: getThemeColor(index), backgroundColor: getThemeColor(index) + '1A' }">
                  <i class="el-icon-medal"></i>
                </div>
                <h3 class="sport-title">{{ sportInfo.sportType }}</h3>
              </div>

              <div class="info-list">
                <div class="info-item">
                  <i class="el-icon-time text-muted"></i>
                  <span class="label">适宜时间：</span>
                  <span class="value">{{ sportInfo.suitableTime }}</span>
                </div>
                <div class="info-item">
                  <i class="el-icon-odometer text-muted"></i>
                  <span class="label">适宜心率：</span>
                  <span class="value">{{ sportInfo.suitableHeartRate }}</span>
                </div>
                <div class="info-item">
                  <i class="el-icon-date text-muted"></i>
                  <span class="label">适宜频率：</span>
                  <span class="value">{{ sportInfo.suitableFrequency }}</span>
                </div>
                <div class="info-item">
                  <i class="el-icon-data-line text-muted"></i>
                  <span class="label">推荐速度：</span>
                  <span class="value">{{ sportInfo.recommendedSpeed }}</span>
                </div>
              </div>

              <div class="card-action">
                <el-button 
                  type="primary" 
                  round 
                  plain 
                  size="small" 
                  class="detail-btn"
                  @click="goToDetail(sportInfo.sportType, sportInfo)"
                >
                  查看详情 <i class="el-icon-arrow-right el-icon--right"></i>
                </el-button>
              </div>
            </div>
          </el-card>
        </el-col>
      </el-row>
      
      <el-empty v-if="sportInfos.length === 0" description="没有找到相关的运动知识哦"></el-empty>
    </div>
  </div>
</template>

<script>
import sportApi from "@/api/Function_Menu";

export default {
  data() {
    return {
      searchText: "",
      sportInfos: [],
      DetailInfo: [],
      // 定义一组活泼的主题色，给不同卡片分配不同颜色
      themeColors: ['#409EFF', '#67C23A', '#E6A23C', '#F56C6C', '#8e44ad', '#1abc9c']
    };
  },

  async created() {
    await this.showSearch();
  },

  methods: {
    // 动态获取卡片主题色
    getThemeColor(index) {
      return this.themeColors[index % this.themeColors.length];
    },

    goToDetail(sportName, sportInfo) {
      sportApi
        .DetailInfo(sportName)
        .then((response) => {
          const detailInfo = response.data;
          const mergedInfo = { ...detailInfo, ...sportInfo };
          console.log(detailInfo);
          this.$router.push({ path: "/detail", query: mergedInfo });
        })
        .catch((error) => {
          console.error(error);
        });
    },

    async showSearch() {
      try {
        const response = await sportApi.getAllSportInfo();
        const sportInfoData = response.data.sportInfos;
        const sportInfos = sportInfoData.slice();
        
        this.sportInfos = sportInfos.map((info) => ({
          id: info.id,
          sportType: info.sportType,
          suitableTime: info.suitableTime,
          suitableHeartRate: info.suitableHeartRate,
          suitableFrequency: info.suitableFrequency,
          recommendedSpeed: info.recommendedSpeed,
        }));
        
        // 初次加载不需要弹出“查询成功”打扰用户，注释掉了 message
      } catch (error) {
        console.log(error);
      }
    },

    async Search() {
      if (!this.searchText.trim()) {
        this.showSearch(); // 如果搜索框为空，重新加载所有数据
        return;
      }
      
      try {
        const response = await sportApi.getAllSportInfo();
        const sportInfoData = response.data.sportInfos;
        
        const filteredSportInfoData = sportInfoData.filter((info) => {
          return info.sportType.includes(this.searchText);
        });
        
        this.sportInfos = filteredSportInfoData.map((info) => ({
          id: info.id,
          sportType: info.sportType,
          suitableTime: info.suitableTime,
          suitableHeartRate: info.suitableHeartRate,
          suitableFrequency: info.suitableFrequency,
          recommendedSpeed: info.recommendedSpeed,
        }));
        
        this.$message({
          message: `找到 ${this.sportInfos.length} 条相关结果`,
          type: "success",
        });
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.app-container {
  min-height: calc(100vh - 84px);
  background-color: #f5f7fa;
  padding-bottom: 40px;
}

/* 顶部搜索区样式 */
.search-header {
  background: linear-gradient(135deg, #ffffff 0%, #f0f4ff 100%);
  padding: 40px 20px;
  text-align: center;
  border-bottom: 1px solid #e4e7ed;
  margin-bottom: 30px;

  .page-title {
    font-size: 28px;
    color: #303133;
    margin: 0 0 10px 0;
    font-weight: 600;
    letter-spacing: 1px;
  }
  
  .page-subtitle {
    font-size: 15px;
    color: #909399;
    margin: 0 0 30px 0;
  }

  .search-box-wrapper {
    max-width: 600px;
    margin: 0 auto;
    
    ::v-deep .main-search-input {
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.05);
      border-radius: 25px;
      overflow: hidden;
      
      .el-input__inner {
        height: 50px;
        border: none;
        padding-left: 20px;
        font-size: 15px;
      }
      .el-input-group__append {
        background-color: #409EFF;
        border: none;
        color: white;
        
        .search-btn {
          font-weight: bold;
          letter-spacing: 2px;
          &:hover { opacity: 0.9; }
        }
      }
    }
  }
}

/* 卡片网格区样式 */
.card-grid {
  padding: 0 24px;
  max-width: 1400px;
  margin: 0 auto;

  .el-col {
    margin-bottom: 24px;
  }

  .sport-card {
    border: none;
    border-radius: 12px;
    position: relative;
    overflow: hidden;
    transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
    
    &:hover {
      transform: translateY(-5px);
      box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
      
      .detail-btn {
        background-color: #409EFF;
        color: white;
      }
    }

    .card-deco-line {
      position: absolute;
      left: 0;
      top: 0;
      bottom: 0;
      width: 4px;
    }

    .card-content {
      padding: 10px 10px 10px 20px;
    }

    .card-header {
      display: flex;
      align-items: center;
      margin-bottom: 20px;

      .icon-circle {
        width: 48px;
        height: 48px;
        border-radius: 50%;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-right: 15px;
        font-size: 24px;
      }

      .sport-title {
        margin: 0;
        font-size: 20px;
        color: #303133;
        font-weight: bold;
      }
    }

    .info-list {
      margin-bottom: 20px;
      
      .info-item {
        margin-bottom: 12px;
        font-size: 14px;
        display: flex;
        align-items: flex-start;
        
        .text-muted {
          margin-top: 3px;
          margin-right: 8px;
          color: #C0C4CC;
        }
        
        .label {
          color: #909399;
          min-width: 75px;
        }
        
        .value {
          color: #606266;
          font-weight: 500;
          flex: 1;
          line-height: 1.4;
        }
      }
    }

    .card-action {
      text-align: right;
      border-top: 1px dashed #ebeef5;
      padding-top: 15px;
      margin-top: 15px;
      
      .detail-btn {
        transition: all 0.3s;
      }
    }
  }
}
</style>