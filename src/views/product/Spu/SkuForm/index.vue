<template>
  <div>
    <el-form ref="form" label-width="80px">
      <el-form-item label="SPU名称">{{ spu.spuName }}</el-form-item>
      <el-form-item label="SKU名称">
        <el-input v-model="skuInfo.skuName" placeholder="sku名称"/>
      </el-form-item>
      <el-form-item label="价格(元)">
        <el-input-number
          v-model="skuInfo.price"
          :precision="2"
          placeholder="价格(元)"
          controls-position="right"
          :min="1"
          :max="99999"
        />
      </el-form-item>
      <el-form-item label="重量(千克)">
        <el-input-number
          v-model="skuInfo.weight"
          :precision="2"
          placeholder="重量(千克)"
          controls-position="right"
          :min="1"
          :max="9999"
        />
      </el-form-item>
      <el-form-item label="规格描述">
        <el-input v-model="skuInfo.skuDesc" placeholder="规格描述" type="textarea" rows="4"/>
      </el-form-item>
      <el-form-item label="平台属性">
        <el-form ref="form" :inline="true" label-width="80px">
          <el-form-item v-for="attr in attrInfoList" :key="attr.id" :label="attr.attrName">
            <el-select v-model="attr.attrAndAttrValue" placeholder="请选择">
              <el-option
                v-for="attrValue in attr.attrValueList"
                :key="attrValue.id"
                :label="attrValue.valueName"
                :value="{'attrId':`${attr.id}`,
                         'valueId':`${attrValue.id}`,
                         'attrName':`${attr.attrName}`,
                         'valueName':`${attrValue.valueName}`}"
              />
            </el-select>
          </el-form-item>
        </el-form>
      </el-form-item>
      <el-form-item label="销售属性">
        <el-form ref="form" :inline="true" label-width="80px">
          <el-form-item v-for="saleAttr in spuSaleAttrList" :key="saleAttr.id" :label="saleAttr.saleAttrName">
            <el-select v-model="saleAttr.saleAttrAndSaleAttrValue" placeholder="请选择">
              <el-option
                v-for="saleAttrValue in saleAttr.spuSaleAttrValueList"
                :key="saleAttrValue.id"
                :label="saleAttrValue.saleAttrValueName"
                :value="{'saleAttrId':`${saleAttr.id}`,
                         'saleAttrValueId':`${saleAttrValue.id}`,
                         'saleAttrName':`${saleAttr.saleAttrName}`,
                         'saleAttrValueName':`${saleAttrValue.saleAttrValueName}`,
                }"
              />
            </el-select>
          </el-form-item>
        </el-form>
      </el-form-item>
      <el-form-item label="图片列表">
        <el-table style="width: 100%" border :data="spuImageList" @selection-change="handleSelectionChange">
          <el-table-column type="selection" align="center" prop="prop" width="80"/>
          <el-table-column prop="prop" label="图片" width="width">
            <template v-slot="{row,$index}">
              <img :src="row.imgUrl" style="width:100px;height:100px">
            </template>
          </el-table-column>
          <el-table-column prop="imgName" label="名称" width="width"/>
          <el-table-column prop="prop" label="操作" width="width">
            <template v-slot="{row,$index}">
              <el-button v-if="row.isDefault===0" type="primary" @click="changeDefault(row)">设置默认</el-button>
              <el-button v-else>默认</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="save">保存</el-button>
        <el-button @click="cancel">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { reqAddSku, reqAttrInfoList, reqSpuImageList, reqSpuSaleAttrList } from '@/api/product/spu'

export default {
  name: 'SkuForm',
  data() {
    return {
      spuImageList: [],
      spuSaleAttrList: [],
      attrInfoList: [],
      // 收集sku数据的字段
      skuInfo: {
        // 第一类收集的数据：父组件给的数据
        category3Id: 0,
        spuId: 0,
        tmId: 0,
        // 第二类：需要通过数据双向绑定v-model收集
        skuName: '',
        price: 0,
        weight: '',
        skuDesc: '',
        // 第三类：需要自己书写代码
        // 默认图片
        skuDefaultImg: '',
        // 收集图片的字段
        skuImageList: [
          // {
          //   imgName: "string",
          //   imgUrl: "string",
          //   isDefault: "string",
          //   spuImgId: 0,
          // },
        ],
        // 平台属性
        skuAttrValueList: [
          // {
          //   attrId: 0,
          //   valueId: 0,
          //   "attrName": "string",
          //   "valueName": "string"
          // },
        ],
        // 销售属性
        skuSaleAttrValueList: [
          // {
          //   saleAttrId: 0,
          //   saleAttrValueId: 0,
          //   "saleAttrName": "string",
          //   "saleAttrValueName": "string",
          // },
        ]
      },
      spu: {},
      // 收集图片的数据字段:但是需要注意，收集的数据目前缺少isDefault字段，将来提交给服务器数据的时候，需要整理参数
      imageList: []
    }
  },
  methods: {
    async getData(category1Id, category2Id, spu) {
      this.spu = spu
      // 收集父组件给予的数据
      this.skuInfo.category3Id = spu.category3Id
      this.skuInfo.spuId = spu.id
      this.skuInfo.tmId = spu.tmId
      const spuImageResult = await reqSpuImageList(spu.id)
      if (spuImageResult.code === 200) {
        const list = spuImageResult.data
        list.forEach(item => {
          item.isDefault = 0
        })
        this.spuImageList = list
      }
      const saleAttrResult = await reqSpuSaleAttrList(spu.id)
      if (saleAttrResult.code === 200) {
        this.spuSaleAttrList = saleAttrResult.data
      }
      const attrInfoResult = await reqAttrInfoList(category1Id, category2Id, spu.category3Id)
      if (attrInfoResult.code === 200) {
        this.attrInfoList = attrInfoResult.data
      }
    },
    handleSelectionChange(value) {
      this.imageList = value
    },
    changeDefault(row) {
      console.log(row)
      this.spuImageList.forEach(item => {
        item.isDefault = 0
      })
      row.isDefault = 1
      this.skuInfo.skuDefaultImg = row.imgUrl
    },
    cancel() {
      this.$emit('changeSceneSkuForm', 0)
      Object.assign(this._data, this.$options.data())
    },
    async save() {
      const { attrInfoList, skuInfo, spuSaleAttrList, imageList } = this
      /* attrInfoList.forEach(item => {
        if (item.attrIdAndAttrValueId) {
          const [attrId, valueId] = item.attrIdAndAttrValueId.split(':')
          skuInfo.skuAttrValueList.push({ attrId, valueId })
        }
      }) */
      skuInfo.skuAttrValueList = attrInfoList.reduce((prev, item) => {
        if (item.attrAndAttrValue.attrId && item.attrAndAttrValue.valueId) {
          const { attrName, valueName } = item.attrAndAttrValue
          const attrId = parseInt(item.attrAndAttrValue.attrId)
          const valueId = parseInt(item.attrAndAttrValue.valueId)
          prev.push({ attrId, valueId, attrName, valueName })
        }
        return prev
      }, [])
      skuInfo.skuSaleAttrValueList = spuSaleAttrList.reduce((prev, item) => {
        if (item.saleAttrAndSaleAttrValue.saleAttrId && item.saleAttrAndSaleAttrValue.saleAttrValueId) {
          const { saleAttrName, saleAttrValueName } = item.saleAttrAndSaleAttrValue
          const saleAttrId = parseInt(item.saleAttrAndSaleAttrValue.saleAttrId)
          const saleAttrValueId = parseInt(item.saleAttrAndSaleAttrValue.saleAttrValueId)
          prev.push({ saleAttrId, saleAttrValueId, saleAttrName, saleAttrValueName })
        }
        return prev
      }, [])
      skuInfo.skuImageList = imageList.map(item => {
        return {
          imgName: item.imgName,
          imgUrl: item.imgUrl,
          isDefault: item.isDefault,
          spuImgId: item.id
        }
      })
      const result = await reqAddSku(skuInfo)
      if (result.code === 200) {
        this.$message({ type: 'success', message: '添加SKU成功' })
        this.$emit('changeSceneSkuForm', 0)
      }
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
