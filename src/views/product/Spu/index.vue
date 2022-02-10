<template>
  <div>
    <el-card style="margin: 20px 0">
      <CategorySelect :show="scene!==0" @getCategoryId="getCategoryId"/>
    </el-card>
    <el-card>
      <div v-show="scene===0">
        <!-- 展示SPU列表的结构 -->
        <el-button
          type="primary"
          icon="el-icon-plus"
          style="margin-bottom: 20px"
          :disabled="!category3Id"
          @click="addSpu"
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
                @click="updateSpu(row)"
              ></hint-button>
              <hint-button
                type="info"
                icon="el-icon-info"
                size="mini"
                title="查看当前spu全部sku列表"
              ></hint-button>
              <el-popconfirm
                title="这是一段内容确定删除吗？"
                @onConfirm="deleteSpu(row)"
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
      <SpuForm v-show="scene===1" ref="spu" @changeScene="changeScene"></SpuForm>
      <SkuForm v-show="scene===2"></SkuForm>
    </el-card>
  </div>
</template>

<script>
import { reqDeleteSpu, reqSpuList } from '@/api/product/spu'
import SpuForm from '@/views/product/Spu/SpuForm'
import SkuForm from '@/views/product/Spu/SkuForm'

export default {
  name: 'Spu',
  components: { SkuForm, SpuForm },
  data() {
    return {
      category1Id: '',
      category2Id: '',
      category3Id: '',
      page: 1,
      limit: 3,
      total: 0,
      pages: 0,
      records: [],
      scene: 0
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
    },
    addSpu() {
      this.scene = 1
      this.$refs.spu.addSpuData(this.category3Id)
    },
    updateSpu(row) {
      this.scene = 1
      this.$refs.spu.initSpuData(row)
    },
    changeScene({ scene, flag }) {
      this.scene = scene
      if (flag === '修改') {
        this.getSpuList(this.page)
      } else if (flag === '添加') {
        this.getSpuList()
      }
    },
    async deleteSpu(row) {
      const result = await reqDeleteSpu(row.id)
      if (result.code === 200) {
        this.$message({ type: 'success', message: '删除成功' })
        // 代表SPU个数大于1删除的时候停留在当前页，如果SPU个数小于1 回到上一页
        this.getSpuList(this.records.length > 1 ? this.page : this.page - 1)
      }
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
