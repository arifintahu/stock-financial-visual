<template>
  <el-card class="box-card">
    <template #header>
      <div class="card-header">
        <span>Fair Value</span>
      </div>
    </template>
    <div class="card-body">
      <div class="input">
        <el-input placeholder="Current price" type="number" v-model="current_price" class="input-width"></el-input>
      </div>
      <div class="result">
        <el-table
          :data="tableData"
          style="width: 100%">
          <el-table-column
            prop="desc"
            label="Description">
          </el-table-column>
          <el-table-column
            prop="value"
            label="Fair Value">
          </el-table-column>
          <el-table-column
            prop="mos"
            label="Margin of Safety (%)">
          </el-table-column>
        </el-table>
      </div>
    </div>
  </el-card>
</template>

<script>

export default {
  name: 'FairValue',
  props: {
    data: Object
  },
  
  data() {
    return {
      current_price: null,
      tableData: []
    }
  },
  methods: {
    updateTable(val) {
      this.tableData = [];
      if (val.mean_per && val.mean_eps) {
        const value = Math.round(val.mean_per*val.mean_eps);
        this.tableData.push({
          desc: 'Price mean PER x mean EPS',
          value: value,
          mos: this.current_price ? this.countmos(value) : ''
        });
      }

      if (val.mean_per && val.last_eps) {
        const value = Math.round(val.mean_per*val.last_eps);
        this.tableData.push({
          desc: 'Price mean PER x last EPS',
          value: value,
          mos: this.current_price ? this.countmos(value) : ''
        });
      }

      if (val.mean_pbv && val.last_bv) {
        const value = Math.round(val.mean_pbv*val.last_bv);
        this.tableData.push({
          desc: 'Price mean PBV x last BV',
          value: value,
          mos: this.current_price ? this.countmos(value) : ''
        });
      }
    },
    countmos(value) {
      const mos = (value-this.current_price)/this.current_price;
      return Math.round(mos*100*100)/100;
    }
  },
  watch: {
    data: {
      handler(val) {
        this.updateTable(val);
      },
      deep: true
    },
    current_price() {
      this.updateTable(this.data);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .card-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 20px;
      font-weight: 600;
  }
  .input-width {
    max-width: 200px;
  }
</style>
