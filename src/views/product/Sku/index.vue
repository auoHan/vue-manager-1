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
      <el-table-column prop="weight" label="重量" width="80" />
      <el-table-column prop="price" label="价格" width="80" />
      <el-table-column prop="prop" label="操作" width="width">
        <template v-slot="{ row, $index }">
          <el-button
            v-if="row.isSale===0"
            type="success"
            icon="el-icon-bottom"
            size="mini"
            @click="sale(row)"
          />
          <el-button
            v-else
            type="success"
            icon="el-icon-top"
            size="mini"
            @click="cancelSale(row)"
          />
          <el-button
            type="primary"
            icon="el-icon-edit"
            size="mini"
            @click="edit"
          />
          <el-button
            type="info"
            icon="el-icon-info"
            size="mini"
            @click="getSkuInfo(row)"
          />
          <el-popconfirm
            title="这是一段内容确定删除吗？"
            @onConfirm="deleteSku(row)"
          >
            <el-button
              slot="reference"
              type="danger"
              icon="el-icon-delete"
              size="mini"
              style="margin-left: 10px"
            />
          </el-popconfirm>
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
    <!-- 抽屉效果 -->
    <el-drawer :visible.sync="show" :show-close="false" size="50%">
      <el-row>
        <el-col :span="5">名称</el-col>
        <el-col :span="16">{{ skuInfo.skuName }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="5">描述</el-col>
        <el-col :span="16">{{ skuInfo.skuDesc }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="5">价格</el-col>
        <el-col :span="16">{{ skuInfo.price }}元</el-col>
      </el-row>
      <el-row>
        <el-col :span="5">平台属性</el-col>
        <el-col :span="16">
          <template>
            <el-tag v-for="attr in skuInfo.skuAttrValueList" :key="attr.id" type="success" style="margin-right:10px">{{ attr.attrName }}-{{ attr.valueName }}</el-tag>
          </template>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="5">商品图片</el-col>
        <el-col :span="16">
          <el-carousel height="450px">
            <el-carousel-item v-for="item in skuInfo.skuImageList" :key="item.id">
              <img :src="item.imgUrl">
            </el-carousel-item>
          </el-carousel>
        </el-col>
      </el-row>
    </el-drawer>
  </div>
</template>

<script>
import { reqCancelSale, reqDeleteSku, reqSale, reqSkuById, reqSkuList } from '@/api/product/sku'

export default {
  name: 'Sku',
  data() {
    return {
      page: 1,
      limit: 3,
      total: 0,
      records: [],
      skuInfo: {},
      show: false
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
    },
    async sale(sku) {
      const result = await reqSale(sku.id)
      if (result.code === 200) {
        // eslint-disable-next-line require-atomic-updates
        sku.isSale = 1
        this.$message({ type: 'success', message: '上架成功' })
      }
      console.log(result)
    },
    async cancelSale(sku) {
      const result = await reqCancelSale(sku.id)
      if (result.code === 200) {
        // eslint-disable-next-line require-atomic-updates
        sku.isSale = 0
        this.$message({ type: 'success', message: '下架成功' })
      }
      console.log(result)
    },
    edit() {
      this.$message('正在开发中')
    },
    async deleteSku(sku) {
      const result = await reqDeleteSku(sku.id)
      if (result.code === 200) {
        this.$message({ type: 'success', message: '删除成功' })
        this.getSkuList(this.records.length > 1 ? this.page : this.page - 1)
      }
    },
    async getSkuInfo(sku) {
      this.show = true;
      const result = await reqSkuById(sku.id)
      if (result.code === 200) {
        this.skuInfo = result.data
      }
    }
  }
}
</script>

<style>
.el-carousel__item h3 {
  color: #475669;
  font-size: 14px;
  opacity: 0.75;
  line-height: 150px;
  margin: 0;
}

.el-carousel__item:nth-child(2n) {
  background-color: #99a9bf;
}

.el-carousel__item:nth-child(2n+1) {
  background-color: #d3dce6;
}
</style>

<style scoped>
.el-row .el-col-5{
  font-size:18px;
  text-align:right;
}
.el-col{
  margin:10px 10px;
}

>>>.el-carousel__button{
  width:10px;
  height:10px;
  background:red;
  border-radius:50%;
}
</style>
