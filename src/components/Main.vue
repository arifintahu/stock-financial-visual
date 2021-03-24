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

      <div class="option" v-if="content.length">
        <el-row :gutter="20">
          <el-col :lg="12" :md="12" :sm="24">
            <div class="option-title">Filter by</div>
            <div class="option-body">
              <el-select v-model="quarter" placeholder="Select Time">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </div>
          </el-col>
          <el-col :lg="12" :md="12" :sm="24">
            <div class="option-title">Stocksplit</div>
            <div class="option-body">
              <el-switch v-model="isStockSplit">
              </el-switch>
              <el-select v-if="isStockSplit" v-model="splitQuarter" placeholder="Select Quarter" class="ml-option">
                <el-option
                  v-for="item in optionQuarter"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
              <el-input v-if="splitQuarter" placeholder="Split Num" v-model="splitNum" class="ml-option"></el-input>
            </div>
          </el-col>
        </el-row>
      </div>

      <div class="graph" v-if="content.length">
        <el-row :gutter="20">
          <el-col :lg="24" :md="24">
            <FairValue :data="harga_wajar"/>
          </el-col>
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
import FairValue from './FairValue.vue';
import xlsx from 'xlsx';

export default {
  name: 'Main',
  components: {
    Table,
    ShowChart,
    FairValue
  },
  data() {
    return {
      rawcontent: [],
      attachments: [],
      fileExcel: null,
      options: [{
          value: 0,
          label: 'All time'
        }, {
          value: -12,
          label: 'Last 3 years'
        }, {
          value: -20,
          label: 'Last 5 years'
        }, {
          value: -40,
          label: 'Last 10 years'
        }],
      quarter: 0,
      isStockSplit: false,
      splitQuarter: null,
      splitNum: null,
      harga_wajar: {
        mean_per: 0,
        mean_pbv: 0,
        mean_eps: 0,
        last_eps: 0,
        last_bv: 0
      }
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
        _this.rawcontent          = xlsx.utils.sheet_to_json(workbook.Sheets[sheet_name_list[0]]);
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
      const mean        = this.arrayMean(series);

      if (key == 'PER') {
        this.harga_wajar.mean_per = mean[0];
      } else if (key == 'PBV(%)') {
        this.harga_wajar.mean_pbv = mean[0];
        this.harga_wajar.last_bv  = content.slice(-1)[0]['BV(Rp)'];
      } else if (key == 'EPS(Rp)') {
        this.harga_wajar.mean_eps = mean[0];
        this.harga_wajar.last_eps = series.slice(-1)[0];
      }

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
            data: mean
          }
        ]
      }
    }
  },
  computed: {
    optionQuarter() {
      return this.rawcontent.map(val => {
        return {
          value: val.Quarter,
          label: val.Quarter
        }
      })
    },
    content() {
      if (this.isStockSplit && this.splitNum) {
        if (isFinite(this.splitNum)) {
          const num           = parseFloat(this.splitNum);
          const content       = this.rawcontent.slice(this.quarter);
          const indexQuarter  = content.findIndex(val => val.Quarter == this.splitQuarter);
          const result = content.map((val, index) => {
            if (index < indexQuarter) {
              return {
                ... val,
                'EPS(Rp)': Math.round(val['EPS(Rp)']*100/num)/100
              }
            } else {
              return {
                ... val,
                'EPS(Rp)': Math.round(val['EPS(Rp)']*100)/100
              };
            }
          });
          return result;
        } else {
          alert("It's not a number");
          return this.rawcontent.slice(this.quarter);
        }
      } else {
        return this.rawcontent.slice(this.quarter);
      }
    },
    data_show_per() {
      const key   = 'PER';
      const title = `Historical ${key}`;
      const data  = this.chartDefault(title, key, this.content);
      return data;
    },
    data_show_pbv() {
      const key   = 'PBV(%)';
      const title = `Historical ${key}`;
      const data  = this.chartDefault(title, key, this.content);
      return data;
    },
    data_show_npm() {
      const key   = 'NPM(%)';
      const title = `Historical ${key}`;
      const data  = this.chartDefault(title, key, this.content);
      return data;
    },
    data_show_roe() {
      const key   = 'ROE(%)';
      const title = `Historical ${key}`;
      const data  = this.chartDefault(title, key, this.content);
      return data;
    },
    data_show_eps() {
      const key   = 'EPS(Rp)';
      const title = `Historical ${key}`;
      const data  = this.chartDefault(title, key, this.content);
      return data;
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
  .option-title {
    font-size: 20px;
    margin-bottom: 12px;
  }
  .ml-option {
    margin-left: 12px;
    width: 120px;
  }
  
</style>
