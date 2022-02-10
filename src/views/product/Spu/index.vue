<template>
  <div>
    <el-card style="margin: 20px 0">
      <CategorySelect :show="!show" @getCategoryId="getCategoryId"/>
    </el-card>
    <el-card>
      <div>
        <!-- 展示SPU列表的结构 -->
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
        >添加SPU
        </el-button
        >
        <el-table style="width: 100%" border :data="records">
          <el-table-column type="index" label="序号" width="80" align="center">
          </el-table-column>
          <el-table-column prop="spuName" label="SPU名称" width="300">
          </el-table-column>
          <el-table-column prop="description" label="SPU描述" width="width">
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row, $index }">
              <!-- 这里按钮将来用hintButton替换 -->
              <hint-button
                type="success"
                icon="el-icon-plus"
                size="mini"
                title="添加sku"
              ></hint-button>
              <hint-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                title="修改spu"
              ></hint-button>
              <hint-button
                type="info"
                icon="el-icon-info"
                size="mini"
                title="查看当前spu全部sku列表"
              ></hint-button>
              <el-popconfirm
                title="这是一段内容确定删除吗？"
              >
                <hint-button
                  slot="reference"
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  title="删除spu"
                ></hint-button>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
          style="text-align: center"
          :current-page="page"
          :page-size="limit"
          :total="total"
          :page-sizes="[3, 5, 10]"
          layout="prev, pager, next, jumper,->, sizes,total"
          @current-change="getSpuList"
          @size-change="handleSizeChange"
        >
        </el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
import { reqSpuList } from '@/api/product/spu'

export default {
  name: 'Spu',
  data() {
    return {
      show: true,
      category1Id: '',
      category2Id: '',
      category3Id: '',
      page: 1,
      limit: 3,
      total: 0,
      pages: 0,
      records: []
    }
  },
  methods: {
    getCategoryId({ categoryId, level }) {
      if (level === 1) {
        this.category1Id = categoryId
        this.category2Id = ''
        this.category3Id = ''
      } else if (level === 2) {
        this.category2Id = categoryId
        this.category3Id = ''
      } else if (level === 3) {
        this.category3Id = categoryId
        this.getSpuList()
      }
    },
    async getSpuList(page1 = 1) {
      this.page = page1
      const { page, limit, category3Id } = this
      const result = await reqSpuList(page, limit, category3Id)
      if (result.code === 200) {
        this.records = result.data.records
        this.total = result.data.total
        this.pages = result.data.pages
      }
    },
    handleSizeChange(limit) {
      this.limit = limit
      this.getSpuList()
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
