
<template>
  <div class="dashboard-container">
    <el-row :gutter="20">
      <el-col :xs="{span:24}" :sm="{span:12}" :md="{span:8}">
        <div class="grid-content bg-purple">
          <svg-icon icon-class="watermelon" />
          <a href="#">西瓜视频解析</a>
        </div>
      </el-col>
      <el-col :xs="{span:24}" :sm="{span:12}" :md="{span:8}">
        <div class="grid-content bg-purple-light">
          <svg-icon icon-class="web-code" />
          <a href="#">web服务器</a>
        </div>
      </el-col>
      <el-col :xs="{span:24}" :sm="{span:12}" :md="{span:8}">
        <div class="grid-content bg-purple">
          <svg-icon icon-class="docker" />
          <a href="#">portainer面板</a></div>
      </el-col>
      <el-col :xs="{span:24}" :sm="{span:12}" :md="{span:8}">
        <div class="grid-content bg-purple-light">
          <svg-icon icon-class="router" /><a href="#">openwrt</a></div>
      </el-col>
      <el-col :xs="{span:24}" :sm="{span:12}" :md="{span:8}">
        <div class="grid-content bg-purple">
          <svg-icon icon-class="heartbeat" /><a href="#">未知</a></div>
      </el-col>
      <el-col :xs="{span:24}" :sm="{span:12}" :md="{span:8}">
        <div class="grid-content bg-purple-light">
          <svg-icon icon-class="heartbeat" /><a href="#">未知</a></div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import { mapGetters } from 'vuex'

  export default {
    name: 'Dashboard',
    computed: {
      ...mapGetters([
        'name'
      ])
    }
  }
</script>

<style lang="scss" scoped>
  .dashboard {
    &-container {
      margin: 30px;
    }

    &-text {
      font-size: 30px;
      line-height: 46px;
    }
  }

  .el-row {
    margin-bottom: 20px;

    &:last-child {
      margin-bottom: 0;
    }
  }

  .el-col {
    border-radius: 4px;
  }

  .bg-purple-dark {
    background: #99a9bf;
  }

  .bg-purple {
    background: #d3dce6;
  }

  .bg-purple-light {
    background: #e5e9f2;
  }

  .grid-content {
    & a {
      color: #1890ff;
      font-size: 18px;
      margin-left: 10px;
    }

    border-radius: 4px;
    min-height: 36px;
    margin-bottom: 20px;
    height: 100px;
    text-align: center;
    line-height: 100px;
    font-size: 30px;
  }

  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
</style>