<template>
  <el-form ref="dataForm" :model="dataForm" :rules="dataRules" label-width="80px"
           style="margin:20px;width:85%;min-width:600px;">
    <el-form-item label="动态内容" prop="content">
      <el-input type="textarea" :rows="10" v-model="dataForm.content"></el-input>
    </el-form-item>
    <el-form-item label="添加图片">
      <el-upload
        action="/activity/message/savepic"
        list-type="picture-card"
        :multiple="true"
        :limit="3"
        :auto-upload="true"
        :file-list="dataForm.messageImgs"
        :on-preview="handlePictureCardPreview"
        :on-remove="handleRemove"
        :on-success="handleAvatarSuccess"
        :before-upload="beforeAvatarUpload"
        :on-exceed="handleExceed"
        :on-error="imgUploadError">
        <i class="el-icon-plus"></i>
        <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过2Mb，最多上传3张照片</div>
      </el-upload>
      <el-dialog :visible.sync="dataForm.dialogVisible">
        <img width="100%" :src="dataForm.activityImg" alt="">
      </el-dialog>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="save()">确认发布</el-button>
      <el-button @click.native.prevent>取消</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
  export default {
    name: 'white',
    data () {
      return {
        dataForm: {
          messageId: '',
          authorId: '',
          authorName: '',
          content: '',
          activityImg: '',
          messageImgs: [],
          dialogVisible: false
        },
        dataRules: {
          content: [{required: true, message: '动态内容不能为空', trigger: 'blur'}]
        }
      }
    },
    methods: {
      handleRemove (file, fileList) {//移除图片
        console.log(file, fileList);
      },
      handlePictureCardPreview (file) {//预览图片
        console.log(file);
        this.dataForm.dialogVisible = true;
        this.dataForm.activityImg = file.url;
      },
      beforeAvatarUpload (file) {//文件上传之前调用做一些拦截限制
        console.log(file)
        const isLt2M = file.size / 1024 / 1024 < 2
        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 2MB!')
        }
        return isLt2M
      },
      handleAvatarSuccess (res, file) {//图片上传成功
        debugger
        console.log(res);
        console.log(file);
        this.dataForm.activityImg = file.url;
      },
      handleExceed (files, fileList) {//图片上传超过数量限制
        this.$message.error('上传图片不能超过3张!')
        console.log(file, fileList)
      },
      imgUploadError (err, file, fileList) {//图片上传失败调用
        console.log(err)
        console.log(fileList)
        this.$message.error('上传图片失败!')
      },
      save () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.dataListLoading = true
            this.$http({
              url: this.$http.adornUrl('/activity/message/save'),
              method: 'post',
              data: this.$http.adornData({
                  'messageId': this.dataForm.messageId,
                  'authorId': this.dataForm.authorId,
                  'authorName': this.dataForm.authorName,
                  'content': this.dataForm.content,
                  'activityImg':this.dataForm.activityImg
                }
                , false)
            }).then(({data}) => {
              debugger
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.dataFormCancel()
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      dataFormCancel () {
        this.dataForm = {}
      },
      onSubmit () {
        console.log('submit!')
      }
    }
  }
</script>

<style scoped>

</style>
