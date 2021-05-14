<template>
  <div class="dashboard-editor-container">
    <el-row>
      <el-col :xs="8" :sm="8" :lg="8">
        <div class="card-panel">
          <div class="card-panel-description">
            <div class="card-panel-text">
              <el-date-picker
                v-model="from_date"
                type="datetime"
                placeholder="From"
              />
            </div>
          </div>
        </div>
      </el-col>
      <el-col :xs="8" :sm="8" :lg="8">
        <div class="card-panel">
          <div class="card-panel-description">
            <el-date-picker
              v-model="to_date"
              type="datetime"
              placeholder="To"
            />
          </div>
        </div>
      </el-col>
      <el-input v-model="apikey" />
      <el-button @click="fetch_data" />
      <!-- <el-form
        action="https://api.data.iotbase.in/idm/oauth2/token"
        method="post"
        @submit.prevent="login"
      >
        <el-input type="text" name="username" value="admin@keyrock" />
        <el-input
          type="text"
          name="password"
          value="password_of_keyrok_admin"
        />
        <el-input type="hidden" name="grant_type" value="password" />
        <el-input type="submit" value="login" />
      </el-form> -->
    </el-row>

    <el-row style="background: #fff; padding: 16px 16px 0; margin-bottom: 32px">
      <line-chart :chart-data="lineChartData" />
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'
// import PanelGroup from './components/PanelGroup'
import LineChart from './components/LineChart'

export default {
  name: 'DashboardAdmin',
  components: {
    // GithubCorner,
    // PanelGroup,
    LineChart
    // RaddarChart,
    // PieChart,
    // BarChart,
    // TransactionTable,
    // TodoList,
    // BoxCard
  },
  data() {
    return {
      lineChartData: {},
      from_date: null,
      to_date: null,
      apikey: '',
      contextResponses: []
    }
  },
  methods: {
    fetch_data: async function() {
      const { status, data } = await axios.get(
        'https://api.data.iotbase.in/comet/STH/v1/contextEntities/type/Value/id/urn:ngsi-ld:Value:001/attributes/Value?lastN=3',
        {
          headers: {
            'X-Auth-Token': this.apikey,
            'fiware-service': 'example',
            'fiware-servicepath': '/',
            Accept: 'application/json'
          }
        }
      )
      console.log(status)
      console.log(data)
      this.contextResponses = data.contextResponses
      this.lineChartData = data.contextResponses[0].contextElement
    }
  }
}
</script>

<style lang="scss" scoped>
.dashboard-editor-container {
  padding: 32px;
  background-color: rgb(240, 242, 245);
  position: relative;

  .github-corner {
    position: absolute;
    top: 0px;
    border: 0;
    right: 0;
  }

  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}

@media (max-width: 1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}
</style>
