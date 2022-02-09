<template>
  <div>
    <el-card style="margin: 20px 0">
      <CategorySelect @getCategoryId="getCategoryId"></CategorySelect>
    </el-card>
    <el-card>
      <el-button type="primary" icon="el-icon-plus" style="margin-bottom: 20px">添加属性</el-button>
      <el-table style="width: 100%" border :data="attrList">
        <el-table-column type="index" label="序号" width="80" align="center">
        </el-table-column>
        <el-table-column prop="attrName" label="属性名称" width="150"></el-table-column>
        <el-table-column prop="prop" label="属性值列表" width="width">
          <template v-slot="{row}">
            <el-tag type="success" v-for="attrValue in row.attrValueList" :key="attrValue.id" style="margin: 0 10px">{{ attrValue.valueName }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="prop" label="操作" width="150">
          <template v-slot="{row}">
            <el-button type="warning" icon="el-icon-edit" size="mini"></el-button>
            <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
          </template>
        </el-table-column>
      </el-table>
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
      attrList: []
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
