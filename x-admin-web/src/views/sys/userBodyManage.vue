<template>
  <div class="app-container">
    <el-card class="search-card" shadow="never">
      <el-form :inline="true" :model="searchModel" class="demo-form-inline">
        <el-form-item label="用户昵称">
          <el-input v-model="searchModel.name" placeholder="请输入昵称" clearable prefix-icon="el-icon-user"></el-input>
        </el-form-item>
        <el-form-item label="用户 ID">
          <el-input v-model="searchModel.id" placeholder="请输入ID" clearable prefix-icon="el-icon-postcard"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getBodyList" type="primary" icon="el-icon-search">查询</el-button>
        </el-form-item>
      </el-form>
    </el-card>

    <el-card class="table-card" shadow="never">
      <el-table 
        :data="bodyList" 
        stripe 
        style="width: 100%" 
        :header-cell-style="{background:'#f5f7fa', color:'#606266', fontWeight:'bold'}"
      >
        <el-table-column type="index" label="序号" width="60" align="center"></el-table-column>
        <el-table-column prop="id" label="ID" width="80" align="center"></el-table-column>
        <el-table-column prop="name" label="昵称" width="120" align="center"></el-table-column>
        <el-table-column prop="age" label="年龄" width="80" align="center"></el-table-column>
        <el-table-column prop="gender" label="性别" width="80" align="center"></el-table-column>
        <el-table-column prop="height" label="身高(cm)" width="100" align="center"></el-table-column>
        <el-table-column prop="weight" label="体重(kg)" width="100" align="center"></el-table-column>
        
        <el-table-column prop="bloodSugar" label="血糖" width="100" align="center"></el-table-column>
        <el-table-column prop="bloodPressure" label="血压" width="120" align="center"></el-table-column>
        <el-table-column prop="bloodLipid" label="血脂" width="100" align="center"></el-table-column>
        <el-table-column prop="heartRate" label="心率(次/分)" width="100" align="center"></el-table-column>
        <el-table-column prop="vision" label="视力" width="80" align="center"></el-table-column>
        
        <el-table-column prop="sleepDuration" label="睡眠时长(h)" width="110" align="center"></el-table-column>
        <el-table-column prop="sleepQuality" label="睡眠质量" width="100" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.sleepQuality == 1" type="success" size="small">好</el-tag>
            <el-tag v-else-if="scope.row.sleepQuality == 2" type="warning" size="small">一般</el-tag>
            <el-tag v-else-if="scope.row.sleepQuality == 3" type="danger" size="small">差</el-tag>
            <span v-else>--</span>
          </template>
        </el-table-column>

        <el-table-column prop="smoking" label="抽烟" width="80" align="center">
          <template slot-scope="scope">
            <el-tag :type="scope.row.smoking ? 'danger' : 'info'" size="small">{{ scope.row.smoking ? '是' : '否' }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="drinking" label="喝酒" width="80" align="center">
          <template slot-scope="scope">
            <el-tag :type="scope.row.drinking ? 'warning' : 'info'" size="small">{{ scope.row.drinking ? '是' : '否' }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="exercise" label="运动" width="80" align="center">
          <template slot-scope="scope">
            <el-tag :type="scope.row.exercise ? 'success' : 'info'" size="small">{{ scope.row.exercise ? '是' : '否' }}</el-tag>
          </template>
        </el-table-column>

        <el-table-column prop="foodTypes" label="喜好食物" width="120" align="center"></el-table-column>
        <el-table-column prop="waterConsumption" label="饮水量(ml)" width="100" align="center"></el-table-column>

        <el-table-column label="操作" width="150" align="center" fixed="right">
          <template slot-scope="scope">
            <el-button @click="openEditUi(scope.row.id)" type="primary" icon="el-icon-edit" size="mini" circle></el-button>
            <el-button @click="deleteBody(scope.row)" type="danger" icon="el-icon-delete" size="mini" circle></el-button>
          </template>
        </el-table-column>
      </el-table>

      <div class="pagination-container">
        <el-pagination
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="searchModel.pageNo"
          :page-sizes="[5, 10, 20, 30]"
          :page-size="searchModel.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        >
        </el-pagination>
      </div>
    </el-card>

    <el-dialog @close="clearForm" :title="title" :visible.sync="dialogFormVisible" width="650px" custom-class="modern-dialog">
      <el-form :model="bodyForm" ref="bodyFormRef" size="small">
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="昵称" prop="name" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.name" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="年龄" prop="age" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.age" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="性别" prop="gender" :label-width="formLabelWidth">
              <el-radio-group v-model="bodyForm.gender">
                <el-radio label="男">男</el-radio>
                <el-radio label="女">女</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="身高 (cm)" prop="height" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.height" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="体重 (kg)" prop="weight" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.weight" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="心率 (次/分)" prop="heartRate" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.heartRate" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="血压" prop="bloodPressure" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.bloodPressure" autocomplete="off" placeholder="例如: 120/80"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="血糖" prop="bloodSugar" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.bloodSugar" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="血脂" prop="bloodLipid" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.bloodLipid" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="视力" prop="vision" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.vision" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="睡眠时长 (h)" prop="sleepDuration" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.sleepDuration" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="睡眠质量" prop="sleepQuality" :label-width="formLabelWidth">
              <el-select v-model="bodyForm.sleepQuality" placeholder="请选择" style="width: 100%">
                <el-option label="好" :value="1"></el-option>
                <el-option label="一般" :value="2"></el-option>
                <el-option label="差" :value="3"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="喜好食物" prop="foodTypes" :label-width="formLabelWidth">
              <el-select v-model="bodyForm.foodTypes" placeholder="请选择" style="width: 100%">
                <el-option label="蔬菜" value="蔬菜"></el-option>
                <el-option label="水果" value="水果"></el-option>
                <el-option label="肉类" value="肉类"></el-option>
                <el-option label="鱼类" value="鱼类"></el-option>
                <el-option label="豆类" value="豆类"></el-option>
                <el-option label="谷物" value="谷物"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="饮水量 (ml)" prop="waterConsumption" :label-width="formLabelWidth">
              <el-input v-model="bodyForm.waterConsumption" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="是否吸烟" prop="smoking" :label-width="formLabelWidth">
              <el-switch v-model="bodyForm.smoking"></el-switch>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="是否喝酒" prop="drinking" :label-width="formLabelWidth">
              <el-switch v-model="bodyForm.drinking"></el-switch>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="是否运动" prop="exercise" :label-width="formLabelWidth">
              <el-switch v-model="bodyForm.exercise"></el-switch>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false" size="small">取 消</el-button>
        <el-button type="primary" @click="updateBody" size="small">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import userApi from "@/api/userManage";

export default {
  data() {
    return {
      bodyForm: {}, 
      bodyList: [],
      formLabelWidth: "90px", // 缩小了 label 宽度以适应双列
      dialogFormVisible: false,
      title: "",
      total: 0,
      searchModel: {
        pageNo: 1,
        pageSize: 10,
      },
    };
  },

  methods: {
    updateBody() {
      this.$refs.bodyFormRef.validate((valid) => {
        if (valid) {
          userApi.updateBody(this.bodyForm).then((response) => {
            this.$message({ message: response.message, type: "success" });
            this.dialogFormVisible = false;
            this.getBodyList();
          });
        } else {
          return false;
        }
      });
    },

    clearForm() {
      this.bodyForm = {};
      this.$refs.bodyFormRef.clearValidate();
    },

    handleSizeChange(pageSize) {
      this.searchModel.pageSize = pageSize;
      this.getBodyList();
    },

    handleCurrentChange(pageNo) {
      this.searchModel.pageNo = pageNo;
      this.getBodyList();
    },

    getBodyList() {
      userApi.getBodyList(this.searchModel).then((response) => {
        this.bodyList = response.data.rows;
        this.total = response.data.total;
      });
    },

    openEditUi(id) {
      this.title = "修改身体信息";
      userApi.getBodyById(id).then((response) => {
        this.bodyForm = response.data;
      });
      this.dialogFormVisible = true;
    },

    deleteBody(body) {
      this.$confirm(`确认删除 ${body.name} 这个身体信息吗？`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          userApi.deleteBodyById(body.id).then((response) => {
            this.$message({ type: "success", message: response.message });
            this.getBodyList();
          });
        })
        .catch(() => {
          this.$message({ type: "info", message: "已取消删除" });
        });
    },
  },

  created() {
    this.getBodyList();
  },
};
</script>

<style lang="scss" scoped>
.app-container {
  padding: 20px;
  background-color: #f0f2f5;
  min-height: calc(100vh - 84px);

  .search-card {
    margin-bottom: 20px;
    border-radius: 8px;
    border: none;
    
    .el-form-item {
      margin-bottom: 0; // 让搜索栏更紧凑
    }
  }

  .table-card {
    border-radius: 8px;
    border: none;
    padding-bottom: 20px;
  }

  .pagination-container {
    margin-top: 20px;
    display: flex;
    justify-content: flex-end; // 分页靠右对齐更符合现代习惯
  }

  /* 弹窗样式优化 */
  ::v-deep .modern-dialog {
    border-radius: 12px;
    
    .el-dialog__header {
      border-bottom: 1px solid #ebeef5;
      padding-bottom: 15px;
    }
    
    .el-dialog__body {
      padding: 30px 20px 10px 20px;
    }
  }
}
</style>