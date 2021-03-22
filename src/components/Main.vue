<template>
  <el-header>
    <div class="header-title">Stock Financial Visualization</div>
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
      <div class="table" v-if="content.length">
        <Table :data="content"/>
      </div>
      <el-divider v-if="content.length"></el-divider>
      <div class="graph" v-if="content.length">
        <el-row :gutter="20">
          <el-col :lg="12" :md="24">
            <ShowChart :data="data_show_per" />
          </el-col>
          <el-col :lg="12" :md="24">
            <ShowChart :data="data_show_pbv" />
          </el-col>
          <el-col :lg="12" :md="24">
            <ShowChart :data="data_show_npm" />
          </el-col>
          <el-col :lg="12" :md="24">
            <ShowChart :data="data_show_roe" />
          </el-col>
          <el-col :lg="24" :md="24">
            <ShowChart :data="data_show_eps" />
          </el-col>
        </el-row>
      </div>
    </el-container>
  </el-main>
</template>

<script>
import Table from './Table.vue';
import ShowChart from './ShowChart.vue';
import xlsx from 'xlsx';

export default {
  name: 'Main',
  components: {
    Table,
    ShowChart
  },
  data() {
    return {
      content: [],
      attachments: [],
      fileExcel: null
    }
  },
  methods: {
    loadData() {
      const reader = new FileReader();
      const _this = this;
      reader.onload = function(e) {
        const data      = e.target.result;
        const workbook  = xlsx.read(data, {
          type: 'binary'
        });
        const sheet_name_list = workbook.SheetNames;
        _this.content          = xlsx.utils.sheet_to_json(workbook.Sheets[sheet_name_list[0]]);
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
    arrayMean(arr) {
      const sum = arr.reduce((acc, val) => {
        acc = acc + val;
        return acc;
      }, 0);
      const avg = Math.round(sum*100/arr.length)/100;
      const result = arr.map(() => {
        if (sum) {
          return avg;
        } else {
          return sum;
        }
      });
      return result;
    },
    chartDefault(title, key, content) {
      const categories  = content.map(val => val['Quarter']);
      const series      = content.map(val => val[key]);
      const code        = content.length ? content[0].Code : '';
      return {
        title: `${title} ${code}`,
        categories: categories,
        series: [
          {
            name: key,
            type: 'line',
            data: series
          },
          {
            name: `Mean ${key}`,
            type: 'area',
            data: this.arrayMean(series)
          }
        ]
      }
    }
  },
  computed: {
    data_show_per() {
      const key   = 'PER';
      const title = `Historical ${key}`;
      return this.chartDefault(title, key, this.content);
    },
    data_show_pbv() {
      const key   = 'PBV(%)';
      const title = `Historical ${key}`;
      return this.chartDefault(title, key, this.content);
    },
    data_show_npm() {
      const key   = 'NPM(%)';
      const title = `Historical ${key}`;
      return this.chartDefault(title, key, this.content);
    },
    data_show_roe() {
      const key   = 'ROE(%)';
      const title = `Historical ${key}`;
      return this.chartDefault(title, key, this.content);
    },
    data_show_eps() {
      const key   = 'EPS(Rp)';
      const title = `Historical ${key}`;
      return this.chartDefault(title, key, this.content);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .container {
    flex-direction: column;
  }
  .header-title {
    font-size: 32px;
    margin-top: 20px;
  }
  .table {
    margin-top: 20px;
  }
  .graph {
    margin-top: 32px;
    width: 100%;
  }
  .box-card {
    width: 100%;
    margin: 10px 0;
  }
  
</style>
