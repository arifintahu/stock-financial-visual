<template>
  <el-header>
    <div>Stock Financial Visualization</div>
  </el-header>
  <el-main>
    <el-container class="container">
      <div class="upload-form">
        <el-upload
          class="upload-demo"
          action=""
          :on-change="handleChange"
          :file-list="attachments"
          :auto-upload="false"
        >
          <template #trigger>
            <el-button size="small" type="primary" >Select File</el-button>
          </template>
          <el-button
            style="margin-left: 10px;"
            size="small"
            type="success"
            @click="loadData"
            >Load Data</el-button
          >
          <template #tip>
            <div class="el-upload__tip">
              csv/xlsx file
            </div>
          </template>
        </el-upload>
      </div>
      <div class="graph">
        <el-row :gutter="20">

          <el-col :lg="12" :md="24">
            <el-card class="box-card">
              <template #header>
                <div class="card-header">
                  <span>Historical PER</span>
                </div>
              </template>
              <div class="card-body">
                <LineChart :categories="categories" :series="series" />
              </div>
            </el-card>
          </el-col>

          <el-col :lg="12" :md="24">
            <el-card class="box-card">
              <template #header>
                <div class="card-header">
                  <span>Historical PER</span>
                </div>
              </template>
              <div class="card-body">
                <LineChart :categories="categories" :series="series" />
              </div>
            </el-card>
          </el-col>

          <el-col :lg="12" :md="24">
            <el-card class="box-card">
              <template #header>
                <div class="card-header">
                  <span>Historical PER</span>
                </div>
              </template>
              <div class="card-body">
                <LineChart :categories="categories" :series="series" />
              </div>
            </el-card>
          </el-col>

          <el-col :lg="12" :md="24">
            <el-card class="box-card">
              <template #header>
                <div class="card-header">
                  <span>Historical PER</span>
                </div>
              </template>
              <div class="card-body">
                <LineChart :categories="categories" :series="series" />
              </div>
            </el-card>
          </el-col>

        </el-row>
      </div>
    </el-container>
  </el-main>
</template>

<script>
import LineChart from './LineChart.vue';
import xlsx from 'xlsx';

export default {
  name: 'Main',
  components: {
    LineChart
  },
  data() {
    return {
      categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
      series: [{
        name: 'series-1',
        data: [30, 40, 45, 50, 49, 60, 70, 91]
      }],
      content: [],
      attachments: [],
      fileExcel: null
    }
  },
  methods: {
    loadData() {
      const reader = new FileReader();
      reader.onload = function(e) {
        const data      = e.target.result;
        const workbook  = xlsx.read(data, {
          type: 'binary'
        });
        const sheet_name_list = workbook.SheetNames;
        const content         = xlsx.utils.sheet_to_json(workbook.Sheets[sheet_name_list[0]]);
        this.content          = content;
        console.log(this.content);
      };
      reader.onerror = function(ex) {
        console.log(ex);
      };
      reader.readAsBinaryString(this.fileExcel);
    },
    handleChange(file) {
      this.attachments.push(file);
      this.fileExcel = file.raw;
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .container {
    flex-direction: column;
  }
  .graph {
    width: 100%;
  }
  .box-card {
    width: 100%;
    margin: 10px 0;
  }
  .card-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
  }
</style>
