<template>
  <el-upload
      class="img-upload"
      ref="upload"
      action="http://localhost:8443/api/admin/content/movie/cover"
      :on-preview="handlePreview"
      :on-remove="handleRemove"
      :before-remove="beforeRemove"
      :on-success="handleSuccess"
      multiple
      with-credentials="true"
      :limit="1"
      :on-exceed="handleExceed"
      :file-list="fileList">
    <el-button size="small" type="primary">Click to upload</el-button>
    <div slot="tip" class="el-upload__tip">Only jpg/png files can be uploaded, and no more than 500kb</div>
  </el-upload>
</template>

<script>
export default {
  name: 'ImgUpload',
  data () {
    return {
      fileList: [],
      url: ''
    }
  },
  mounted() {
    this.clear()
  },
  methods: {
    handleRemove (file, fileList) {
    },
    handlePreview (file) {
    },
    handleExceed (files, fileList) {
      this.$message.warning(`当前限制选择 1 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`)
    },
    beforeRemove (file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`)
    },
    handleSuccess (response) {
      this.url = response
      this.$emit('onUpload')
      this.$message.warning('上传成功')
    },
    clear () {
      this.$refs.upload.clearFiles()
    }
  }
}
</script>
