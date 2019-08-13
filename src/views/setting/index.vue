<template>
  <div class="container">
    <el-card>
      <div slot="header">
        <my-bread>个人设置</my-bread>
      </div>
      <!-- 栅格系统 -->
      <el-row>
        <el-col :span="12">
          <!-- 表单 -->
          <el-form label-width="120px">
            <el-form-item label="编号：">{{userForm.id}}</el-form-item>
            <el-form-item label="手机：">{{userForm.mobile}}</el-form-item>
            <el-form-item label="媒体名称：">
              <el-input v-model="userForm.name"></el-input>
            </el-form-item>
            <el-form-item label="媒体介绍：">
              <el-input type="textarea" :rows="3" v-model="userForm.intro"></el-input>
            </el-form-item>
            <el-form-item label="邮箱：">
              <el-input v-model="userForm.email"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="saveUserInfo">保存设置</el-button>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :span="12">
          <!-- 上传头像 -->
          <el-upload
            class="avatar-uploader"
            action
            :http-request="myUpload"
            :show-file-list="false"
          >
            <img v-if="userForm.photo" :src="userForm.photo" class="avatar" />
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
          <div style="text-align:center;font-size:14px">修改头像</div>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
import store from '@/store'
import eventBus from '@/components/eventBus.js'
export default {
  data () {
    return {
      userForm: {
        id: null,
        name: null,
        intro: null,
        email: null,
        photo: null,
        mobile: null
      }
    }
  },
  created () {
    this.getUseInfo()
  },
  methods: {
    myUpload (result) {
      // 选中图片后 触发函数  选择的结果result
      // 文件信息获取 result.file
      // axios + formdata 进行上传
      // console.log(result)

      const formData = new FormData()
      formData.append('photo', result.file)
      this.$http.patch('user/photo', formData).then(res => {
        // 上传成功
        this.$message.success('修改头像成功')
        // 预览
        this.userForm.photo = res.data.data.photo// res.data响应主体
        // 同步本地存储
        store.setUser({ photo: this.userForm.photo })
        // 同步HOME组件
        eventBus.$emit('updtaPhoto', this.userForm.photo)
      })
    },
    // 保存用户信息
    async saveUserInfo () {
      // patch 请求类型做修改 局部修改
      // put 请求类型做修改 完整修改
      await this.$http.patch('user/profile', {
        // 按照后台需要提交数据
        name: this.userForm.name,
        intro: this.userForm.intro,
        email: this.userForm.email
      })
      this.$message.success('保存设置成功')
      // 当你刷新页面的时候 HOME组件使用的本地存储的数据  更新本地存储
      store.setUser({ name: this.userForm.name })
      // 更新HOME组件用户名
      eventBus.$emit('updtaName', this.userForm.name)
    },
    // 获取用户信息
    async getUseInfo () {
      // 后台的头像可能和前端本地保存头像 不一致。保存后进行过修改,重新登录可同步。
      const { data: { data } } = await this.$http.get('user/profile')
      // 获取用户数据
      this.userForm = data
    }
  }
}
</script>

<style scoped lang='less'>
</style>
