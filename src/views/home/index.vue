<template>
  <div class="app-container">
    <div class="total-layout">
      <el-row :gutter="20">
        <el-col :span="8" v-for="(item, index) in statistics" :key="index">
          <div class="total-frame">
            <div class="total-title">{{ item.title }}</div>
            <div class="total-value">{{ item.value }}</div>
          </div>
        </el-col>
      </el-row>
    </div>

    <div class="un-handle-layout">
      <div class="layout-title">待处理事务</div>
      <div class="un-handle-content">
        <el-row :gutter="20">
          <el-col :span="6" v-for="(task, index) in todoList" :key="index">
            <div class="un-handle-item">
              <span>{{ task.name }}</span>
              <span style="float: right" class="color-danger">({{ task.count }})</span>
            </div>
          </el-col>
        </el-row>
      </div>
    </div>

    <div class="statistics-layout">
      <div class="layout-title">业务趋势统计</div>
      <div style="padding: 20px">
        <el-date-picker
          v-model="dateRange"
          type="daterange"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          @change="fetchChartData">
        </el-date-picker>
        <div class="chart-container">
          <ve-line :data="chartData" :loading="loading" :settings="chartSettings"></ve-line>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      // 统计数字绑定
      statistics: [
        { title: '今日订单', value: 0 },
        { title: '今日销售额', value: '￥0.00' },
        { title: '昨日销售额', value: '￥0.00' }
      ],
      // 待处理任务绑定
      todoList: [
        { name: '待付款', count: 0 },
        { name: '待发货', count: 0 },
        { name: '待处理退款', count: 0 }
      ],
      // 图表相关
      dateRange: [],
      loading: false,
      chartSettings: { labelMap: { 'count': '数量', 'amount': '金额' } },
      chartData: {
        columns: ['date', 'count', 'amount'],
        rows: []
      }
    }
  },
  created() {
    this.initDate();
    this.fetchData();
  },
  methods: {
    // 初始化日期范围（默认最近一周）
    initDate() {
      const start = new Date();
      start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
      this.dateRange = [start, new Date()];
    },
    // 调用 API 获取首页概览数据
    async fetchData() {
      console.log('此处替换为你的 API 请求逻辑');
      // 例: const res = await getHomeData();
    },
    // 日期改变时刷新图表
    async fetchChartData() {
      this.loading = true;
      console.log('按日期查询:', this.dateRange);
      // 模拟请求延迟
      setTimeout(() => { this.loading = false; }, 500);
    }
  }
}
</script>

<style scoped>
  .app-container { margin: 20px; }
  .total-layout { margin-bottom: 20px; }
  .total-frame { border: 1px solid #DCDFE6; padding: 20px; text-align: center; }
  .total-title { font-size: 16px; color: #909399; margin-bottom: 10px; }
  .total-value { font-size: 24px; color: #606266; }

  .un-handle-layout { border: 1px solid #DCDFE6; margin-bottom: 20px; }
  .layout-title { background: #F2F6FC; padding: 15px 20px; font-weight: bold; color: #606266; }
  .un-handle-content { padding: 20px; }
  .un-handle-item { border-bottom: 1px solid #EBEEF5; padding: 15px 0; }

  .statistics-layout { border: 1px solid #DCDFE6; }
  .chart-container { margin-top: 20px; min-height: 300px; }
  .color-danger { color: #F56C6C; }
</style>