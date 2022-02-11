<template>
  <div>
    <el-form ref="form" label-width="80px">
      <el-form-item label="SPU名称">qqq</el-form-item>
      <el-form-item label="SKU名称">
        <el-input placeholder="sku名称" />
      </el-form-item>
      <el-form-item label="价格(元)">
        <el-input placeholder="价格(元)" type="number" />
      </el-form-item>
      <el-form-item label="重量(千克)">
        <el-input placeholder="重量(千克)" />
      </el-form-item>
      <el-form-item label="规格描述">
        <el-input placeholder="规格描述" type="textarea" rows="4" />
      </el-form-item>
      <el-form-item label="平台属性">
        <el-form ref="form" :inline="true" label-width="80px">
          <el-form-item label="label">
            <el-select placeholder="请选择" value="">
              <el-option label="label" value="" />
            </el-select>
          </el-form-item>
        </el-form>
      </el-form-item>
      <el-form-item label="销售属性">
        <el-form ref="form" :inline="true" label-width="80px">
          <el-form-item label="label">
            <el-select placeholder="请选择" value="">
              <el-option label="label" value="" />
            </el-select>
          </el-form-item>
        </el-form>
      </el-form-item>
      <el-form-item label="图片列表">
        <!--@selection-change="handleSelectionChange"-->
        <el-table style="width: 100%" border>
          <el-table-column type="selection" align="center" prop="prop" width="80" />
          <el-table-column prop="prop" label="图片" width="width">
            <template slot-scope="{row,$index}">
              <img :src="row.imgUrl" style="width:100px;height:100px">
            </template>
          </el-table-column>
          <el-table-column prop="imgName" label="名称" width="width" />
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{row,$index}">
              <el-button v-if="row.isDefault===0" type="primary">设置默认</el-button>
              <el-button v-else>默认</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-button type="primary">保存</el-button>
        <el-button>取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: 'SkuForm'
}
</script>

<style lang="scss" scoped>

</style>
