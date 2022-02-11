<template>
  <div>
    <el-card style="margin: 20px 0">
      <CategorySelect :show="scene!==0" @getCategoryId="getCategoryId" />
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
        </el-button>
        <el-table style="width: 100%" border :data="records">
          <el-table-column type="index" label="序号" width="80" align="center" />
          <el-table-column prop="spuName" label="SPU名称" width="300" />
          <el-table-column prop="description" label="SPU描述" width="width" />
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row, $index }">
              <!-- 这里按钮将来用hintButton替换 -->
              <hint-button
                type="success"
                icon="el-icon-plus"
                size="mini"
                title="添加sku"
                @click="addSku(row)"
              />
              <hint-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                title="修改spu"
                @click="updateSpu(row)"
              />
              <hint-button
                type="info"
                icon="el-icon-info"
                size="mini"
                title="查看当前spu全部sku列表"
                @click="handler(row)"
              />
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
                />
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
        />
      </div>
      <SpuForm v-show="scene===1" ref="spu" @changeScene="changeScene" />
      <SkuForm v-show="scene===2" ref="sku" @changeSceneSkuForm="changeSceneSkuForm" />
    </el-card>
    <el-dialog
      :title="`${spu.spuName}的sku列表`"
      :visible.sync="dialogTableVisible"
      :before-close="close"
    >
      <!-- table展示sku列表-->
      <el-table v-loading="loading" :data="skuList" style="width: 100%" border>
        <el-table-column prop="skuName" label="名称" width="width" />
        <el-table-column prop="price" label="价格" width="width" />
        <el-table-column prop="weight" label="重量" width="width" />
        <el-table-column label="默认图片" width="width">
          <template v-slot="{row,$index}">
            <img :src="row.skuDefaultImg" alt="" style="width:100px;height:100px;">
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script>
import { reqDeleteSpu, reqSkuList, reqSpuList } from '@/api/product/spu'
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
      scene: 0, // 0代表展示SPU列表数据   1 添加SPU|修改SPU   2 添加SKU
      // 控制对话框的显示与隐藏
      dialogTableVisible: false,
      spu: {},
      skuList: [], // 存储的是SKU列表的数据
      loading: true
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
    },
    addSku(row) {
      this.scene = 2
      this.$refs.sku.getData(this.category1Id, this.category2Id, row)
    },
    changeSceneSkuForm(scene) {
      this.scene = scene
    },
    async handler(spu) {
      // 点击这个按钮的时候，对话框可见的
      this.dialogTableVisible = true
      // 保存spu信息
      this.spu = spu
      // 获取sku列表的数据进行展示
      const result = await reqSkuList(spu.id)
      if (result.code === 200) {
        this.skuList = result.data
        // loading隐藏
        this.loading = false
      }
    },
    //关闭对话框的回调
    close(done){
      //loading属性再次变为真
      this.loading = true;
      //清除sku列表的数据
      this.skuList = [];
      //管理对话框
      done();
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
