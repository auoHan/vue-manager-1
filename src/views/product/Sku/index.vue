<template>
  <div>
    <!-- 表格 -->
    <el-table style="width: 100%" border :data="records">
      <el-table-column
        type="index"
        label="序号"
        width="80"
        align="center"
      />
      <el-table-column
        prop="skuName"
        label="名称"
        width="width"
      />
      <el-table-column
        prop="skuDesc"
        label="描述"
        width="width"
      />
      <el-table-column label="默认图片" width="110">
        <template slot-scope="{ row, $index }">
          <img
            :src="row.skuDefaultImg"
            alt=""
            style="width: 80px; height: 80px"
          >
        </template>
      </el-table-column>
      <el-table-column prop="weight" label="重量" width="80"/>
      <el-table-column prop="price" label="价格" width="80"/>
      <el-table-column prop="prop" label="操作" width="width">
        <template v-slot="{ row, $index }">
          <el-button
            type="success"
            icon="el-icon-sort-down"
            size="mini"
          />
          <el-button
            type="success"
            icon="el-icon-sort-up"
            size="mini"
          />
          <el-button
            type="primary"
            icon="el-icon-edit"
            size="mini"
          />
          <el-button
            type="info"
            icon="el-icon-info"
            size="mini"
          />
          <el-button
            type="danger"
            icon="el-icon-delete"
            size="mini"
          />
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      style="text-align: center"
      :current-page="page"
      :page-sizes="[3, 5, 10]"
      :page-size="limit"
      layout="prev, pager, next, jumper,->,sizes,total"
      :total="total"
      @current-change="getSkuList"
      @size-change="handleSizeChange"
    />
  </div>
</template>

<script>
import { reqSkuList } from '@/api/product/sku'

export default {
  name: 'Sku',
  data() {
    return {
      page: 1,
      limit: 3,
      total: 0,
      records: []
    }
  },
  mounted() {
    this.getSkuList()
  },
  methods: {
    async getSkuList(page1 = 1) {
      this.page = page1
      const { page, limit } = this
      const result = await reqSkuList(page, limit)
      if (result.code === 200) {
        this.records = result.data.records
        this.total = result.data.total
      }
    },
    handleSizeChange(limit) {
      this.limit = limit
      this.getSkuList()
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
