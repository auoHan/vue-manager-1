<template>
  <div>
    <el-card style="margin: 20px 0">
      <CategorySelect @getCategoryId="getCategoryId"/>
    </el-card>
    <el-card>
      <div v-show="isShowTable">
        <el-button type="primary" icon="el-icon-plus" style="margin-bottom: 20px" :disabled="!category3Id" @click="isShowTable=false">添加属性</el-button>
        <el-table style="width: 100%" border :data="attrList">
          <el-table-column type="index" label="序号" width="80" align="center"/>
          <el-table-column prop="attrName" label="属性名称" width="150"/>
          <el-table-column prop="prop" label="属性值列表" width="width">
            <template v-slot="{row}">
              <el-tag v-for="attrValue in row.attrValueList" :key="attrValue.id" type="success" style="margin: 0 10px">
                {{ attrValue.valueName }}
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="150">
            <template v-slot="{row}">
              <el-button type="warning" icon="el-icon-edit" size="mini" @click="isShowTable=false"/>
              <el-button type="danger" icon="el-icon-delete" size="mini"/>
            </template>
          </el-table-column>
        </el-table>
      </div>

      <div v-show="!isShowTable">
        <el-form :inline="true" label-width="80px" >
          <el-form-item label="属性名">
            <el-input
              placeholder="请输入属性名"
            ></el-input>
          </el-form-item>
        </el-form>
        <el-button
          type="primary"
          icon="el-icon-plus"
        >添加属性值</el-button
        >
        <el-button @click="isShowTable = true">取消</el-button>
        <el-table
          style="width: 100%; margin: 20px 0px"
          border
        >
          <el-table-column align="center" type="index" label="序号" width="80">
          </el-table-column>
          <el-table-column width="width" prop="prop" label="属性值名称">
            <template v-slot="{ row }">
              <!-- 这里结构需要用到span与input进行来回的切换 -->
            </template>
          </el-table-column>
          <el-table-column width="width" prop="prop" label="操作">
          </el-table-column>
        </el-table>
        <el-button type="primary">保存</el-button>
        <el-button @click="isShowTable = true">取消</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
import { reqAttrList } from '@/api/product/attr'

export default {
  name: 'Attr',
  data() {
    return {
      category1Id: '',
      category2Id: '',
      category3Id: '',
      attrList: [],
      isShowTable: false
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
        this.getAttrList()
      }
    },
    async getAttrList() {
      const result = await reqAttrList(this.category1Id, this.category2Id, this.category3Id)
      if (result.code === 200) {
        this.attrList = result.data
      }
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
