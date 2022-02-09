<template>
  <div>
    <el-card style="margin: 20px 0">
      <CategorySelect :show="!isShowTable" @getCategoryId="getCategoryId"/>
    </el-card>
    <el-card>
      <div v-show="isShowTable">
        <el-button
          type="primary"
          icon="el-icon-plus"
          style="margin-bottom: 20px"
          :disabled="!category3Id"
          @click="addAttr"
        >添加属性
        </el-button>
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
              <el-button type="warning" icon="el-icon-edit" size="mini" @click="updateAttr(row)"/>
              <el-button type="danger" icon="el-icon-delete" size="mini" @click="deleteAttr(row)"/>
            </template>
          </el-table-column>
        </el-table>
      </div>

      <div v-show="!isShowTable">
        <el-form ref="attrInfo" :inline="true" label-width="80px" :model="attrInfo">
          <el-form-item label="属性名">
            <el-input
              v-model="attrInfo.attrName"
              placeholder="请输入属性名"
            />
          </el-form-item>
        </el-form>
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!attrInfo.attrName.trim()"
          @click="addAttrValue"
        >添加属性值
        </el-button>
        <el-button @click="isShowTable = true">取消</el-button>
        <el-table
          style="width: 100%; margin: 20px 0"
          border
          :data="attrInfo.attrValueList"
        >
          <el-table-column align="center" type="index" label="序号" width="80"/>
          <el-table-column width="width" prop="prop" label="属性值名称">
            <template v-slot="{ row, $index}">
              <!-- 这里结构需要用到span与input进行来回的切换 -->
              <el-input
                v-if="row.flag"
                :ref="$index"
                v-model="row.valueName"
                placeholder="请输入属性值"
                size="mini"
                @blur="toLook(row)"
                @keyup.native.enter="toLook(row)"
              />
              <span v-else style="display: block" @click="toEdit(row,$index)">{{ row.valueName }}</span>
            </template>
          </el-table-column>
          <el-table-column width="width" prop="prop" label="操作">
            <template v-slot="{row,$index}">
              <el-popconfirm
                :title="`确定删除${row.valueName}吗？`"
                @onConfirm="deleteAttrValue($index)"
              >
                <el-button slot="reference" type="danger" size="mini" icon="el-icon-delete"/>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-button type="primary" :disabled="attrInfo.attrValueList.length<1" @click="addOrUpdateAttr">保存</el-button>
        <el-button @click="isShowTable = true">取消</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
import { reqAddOrUpdateAttr, reqAttrList, reqDeleteAttr} from '@/api/product/attr'
// 深拷贝
import cloneDeep from 'lodash/cloneDeep'
import { reqDeleteTradeMark } from '@/api/product/tradeMark'

export default {
  name: 'Attr',
  data() {
    return {
      category1Id: '',
      category2Id: '',
      category3Id: '',
      attrList: [],
      isShowTable: true,
      attrInfo: {
        attrName: ''.trim(),
        attrValueList: [],
        categoryId: 0,
        categoryLevel: 3
      }
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
    },
    addAttrValue() {
      this.attrInfo.attrValueList.push({
        attrId: this.attrInfo.id,
        valueName: ''.trim(),
        flag: true
      })
      this.$nextTick(() => {
        this.$refs[this.attrInfo.attrValueList.length - 1].focus()
      })
    },
    addAttr() {
      this.isShowTable = false
      this.attrInfo = {
        attrName: ''.trim(),
        attrValueList: [],
        categoryId: this.category3Id,
        categoryLevel: 3
      }
    },
    updateAttr(row) {
      this.isShowTable = false
      // 深拷贝
      this.attrInfo = cloneDeep(row)
      this.attrInfo.attrValueList.forEach(item => {
        this.$set(item, 'flag', false)
      })
    },
    toLook(row) {
      if (row.valueName.trim() === '') {
        this.$message('请你输入一个正常的属性值')
        return
      }
      const isRepat = this.attrInfo.attrValueList.some(item => {
        if (row !== item) {
          return row.valueName === item.valueName
        }
      })
      if (isRepat) {
        this.$message('属性值重复了')
        return
      }
      row.flag = false
    },
    toEdit(row, index) {
      row.flag = true
      this.$nextTick(() => {
        this.$refs[index].focus()
      })
    },
    deleteAttrValue(index) {
      this.attrInfo.attrValueList.splice(index, 1)
    },
    addOrUpdateAttr() {
      this.attrInfo.attrValueList = this.attrInfo.attrValueList.filter(async item => {
        if (item.valueName.trim() !== '') {
          delete item.flag
          await reqAddOrUpdateAttr(this.attrInfo)
          this.isShowTable = true
          this.$message({ type: 'success', message: '保存成功' })
          await this.getAttrList()
          return true
        } else {
          this.$message({ type: 'error', message: '保存失败' })
          return false
        }
      })
    },
    deleteAttr(row) {
      this.$confirm(`你确定删除${row.attrName}？`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async() => {
        const result = await reqDeleteAttr(row.id)
        if (result.code === 200) {
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          await this.getAttrList()
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
