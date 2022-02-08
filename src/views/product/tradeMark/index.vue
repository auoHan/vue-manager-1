<template>
  <div>
    <el-button type="primary" icon="el-icon-plus" style="margin: 10px 0">添加</el-button>
    <el-table style="width: 100%" border :data="list">
      <el-table-column type="index" label="序号" width="80px" align="center" />
      <el-table-column prop="tmName" label="品牌名称" width="width" />
      <el-table-column prop="logoUrl" label="品牌LOGO" width="width">
        <template v-slot="{row}">
          <img :src="row.logoUrl" style="width: 100px;height: 100px" :alt="row.tmName">
        </template>
      </el-table-column>
      <el-table-column prop="prop" label="操作" width="width">
        <template v-slot="{row}">
          <el-button type="warning" icon="el-icon-edit" size="mini">修改</el-button>
          <el-button type="danger" icon="el-icon-delete" size="mini">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      style="margin-top: 20px;text-align: center"
      :current-page="page"
      :total="total"
      :page-size="limit"
      :page-count="7"
      :page-sizes="[3,5,10]"
      layout="prev, pager, next, jumper, ->,sizes,total"
      @current-change="getPageList"
      @size-change="handleSizeChange"
    />
  </div>
</template>

<script>
import { reqTradeMarkList } from '@/api/product/tradeMark'

export default {
  name: 'TradeMark',
  data() {
    return {
      page: 1,
      limit: 3,
      total: 0,
      list: []
    }
  },
  mounted() {
    this.getPageList()
  },
  methods: {
    async getPageList(pager = 1) {
      this.page = pager
      const { page, limit } = this
      const result = await reqTradeMarkList(page, limit)
      if (result.code === 200) {
        this.total = result.data.total
        this.list = result.data.records
      }
    },
    handleSizeChange(limit) {
      this.limit = limit
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
