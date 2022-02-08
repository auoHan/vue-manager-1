<template>
  <div>
    <el-button type="primary" icon="el-icon-plus" style="margin: 10px 0" @click="addBrand">添加</el-button>
    <el-table style="width: 100%" border :data="list">
      <el-table-column type="index" label="序号" width="80px" align="center"/>
      <el-table-column prop="tmName" label="品牌名称" width="width"/>
      <el-table-column prop="logoUrl" label="品牌LOGO" width="width">
        <template v-slot="{row}">
          <img :src="row.logoUrl" style="width: 100px;height: 100px" :alt="row.tmName">
        </template>
      </el-table-column>
      <el-table-column prop="prop" label="操作" width="width">
        <template v-slot="{row}">
          <el-button type="warning" icon="el-icon-edit" size="mini" @click="updateTradeMark(row)">修改</el-button>
          <el-button type="danger" icon="el-icon-delete" size="mini">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      style="margin-top: 20px;text-align: center"
      :current-page="page"
      :total="total"
      :page-size="limit"
      :page-count="7"
      :page-sizes="[3,5,10]"
      layout="prev, pager, next, jumper, ->,sizes,total"
      @current-change="getPageList"
      @size-change="handleSizeChange"
    />

    <el-dialog :title="tmForm.id ? '修改品牌' : '添加品牌'" :visible.sync="dialogFormVisible">
      <el-form style="width: 80%" :model="tmForm">
        <el-form-item label="品牌名称" label-width="100px">
          <el-input v-model="tmForm.tmName" autocomplete="off"/>
        </el-form-item>
        <el-form-item label="品牌LOGO" label-width="100px">
          <el-upload
            class="avatar-uploader"
            action="/dev-api/admin/product/fileUpload"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
          >
            <img v-if="tmForm.logoUrl" :src="tmForm.logoUrl" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"/>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过2MB</div>
          </el-upload>

        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addOrUpdateTradeMark">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { reqAddOrUpdateTradeMark, reqTradeMarkList } from '@/api/product/tradeMark'

export default {
  name: 'TradeMark',
  data() {
    return {
      page: 1,
      limit: 3,
      total: 0,
      pages: 0,
      list: [],
      dialogFormVisible: false,
      tmForm: {
        tmName: '',
        logoUrl: ''
      }
    }
  },
  mounted() {
    this.getPageList()
  },
  methods: {
    async getPageList(pager = 1) {
      this.page = pager
      const { page, limit } = this
      const result = await reqTradeMarkList(page, limit)
      if (result.code === 200) {
        this.total = result.data.total
        this.list = result.data.records
        this.pages = result.data.pages
      }
    },
    handleSizeChange(limit) {
      this.limit = limit
    },
    addBrand() {
      this.dialogFormVisible = true
      this.tmForm = { tmName: '', logoUrl: '' }
    },
    updateTradeMark(row) {
      this.dialogFormVisible = true
      // 浅拷贝
      this.tmForm = { ...row }
    },
    handleAvatarSuccess(res, file) {
      this.tmForm.logoUrl = res.data
    },
    beforeAvatarUpload(file) {
      const isJPGOrPNG = file.type === 'image/jpeg' || 'image/png'
      const isLt2M = file.size / 1024 / 1024 < 2
      if (!isJPGOrPNG) {
        this.$message.error('上传头像图片只能是 JPG 或者 PNG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
      }
      return isJPGOrPNG && isLt2M
    },
    async addOrUpdateTradeMark() {
      this.dialogFormVisible = false
      const result = await reqAddOrUpdateTradeMark(this.tmForm)
      console.log(result)
      if (result.code === 200) {
        this.$message({
          type: 'success',
          message: this.tmForm.id ? '修改品牌成功' : '添加品牌成功'
        })
        await this.getPageList(this.tmForm.id ? this.page : this.pages)
      }
    }
  }
}
</script>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}

.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>
