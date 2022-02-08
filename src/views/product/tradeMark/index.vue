<template>
  <div>
    <el-button type="primary" icon="el-icon-plus" style="margin: 10px 0">添加</el-button>
    <el-table style="width: 100%" border>
      <el-table-column prop="prop" label="序号" width="80px" align="center" />
      <el-table-column prop="prop" label="品牌名称" width="width" />
      <el-table-column prop="prop" label="品牌LOGO" width="width" />
      <el-table-column prop="prop" label="操作" width="width" />
    </el-table>
    <el-pagination
      style="margin-top: 20px;text-align: center"
      :current-page="6"
      :total="99"
      :page-size="3"
      :page-count="9"
      :page-sizes="[3,5,10]"
      layout="prev, pager, next, jumper, ->,sizes,total"
    />
  </div>
</template>

<script>
export default {
  name: 'TradeMark'
}
</script>

<style lang="scss" scoped>

</style>
