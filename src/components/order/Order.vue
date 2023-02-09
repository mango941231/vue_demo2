<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>订单管理</el-breadcrumb-item>
      <el-breadcrumb-item>订单列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区 -->
    <el-card>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input
            placeholder="请输入内容"
            v-model="queryInfo.query"
            clearable
            @clear="getOrdersList"
          >
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="getOrdersList"
            ></el-button>
          </el-input>
        </el-col>
      </el-row>
      <!-- table表格区 -->
      <el-table :data="orderslist" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="订单编号" prop="order_number"></el-table-column>
        <el-table-column
          label="订单价格"
          prop="order_price"
          width="150px"
        ></el-table-column>
        <el-table-column label="是否支付" prop="pay_status" width="150px">
          <template slot-scope="scope">
            <el-tag
              type="success"
              effect="plain"
              v-if="scope.row.order_pay === '1'"
              >已付款</el-tag
            >
            <el-tag type="danger" v-else>未付款</el-tag>
          </template>
        </el-table-column>
        <el-table-column
          label="是否发货"
          prop="is_send"
          width="150px"
        ></el-table-column>
        <el-table-column label="下单时间" prop="create_time" width="200px">
          <template slot-scope="scope">
            {{ scope.row.create_time | dateFormat }}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="150px">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit" circle></el-button>
            <el-button
              type="success"
              icon="el-icon-location"
              circle
              @click="getActiveVisible(scope.row.order_id)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!--分页区-->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[1, 2, 5, 10]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      >
      </el-pagination>
    </el-card>
    <el-dialog title="物流进度" :visible.sync="orderActiveVisible" width="50%">
      <el-timeline :reverse="false">
        <el-timeline-item
          v-for="(activity, index) in activities"
          :key="index"
          :timestamp="activity.time"
        >
          {{ activity.context }}
        </el-timeline-item>
      </el-timeline>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      queryInfo: {
        query: "",
        pagenum: 1,
        pagesize: 10,
      },
      orderslist: [],
      total: 0,
      activities: [],
      orderActiveVisible: false
    };
  },
  created() {
    this.getOrdersList();
  },
  methods: {
    async getOrdersList() {
      const { data: res } = await this.$http.get("orders", {
        params: this.queryInfo,
      });
      if (res.meta.status !== 200) {
        return this.$message.error("获取订单列表失败！");
      }
      console.log(res.data);
      this.orderslist = res.data.goods;
      this.total = res.data.total;
    },
    handleSizeChange(pagesize) {
      this.queryInfo.pagesize = pagesize;
      this.getOrdersList();
    },
    handleCurrentChange(pagenum) {
      this.queryInfo.pagenum = pagenum;
      this.getOrdersList();
    },
    async getActiveVisible(id) {
      const { data: res } = await this.$http.get(`/kuaidi/1106975712662`);
      if (res.meta.status !== 200) {
        return this.$message.error("获取订单物流信息失败！");
      }
      console.log(res.data);
      this.activities = res.data
      this.orderActiveVisible = true
    },
  },
};
</script>

<style lang="less" scoped>
</style>