<template>
  <div class="container">
    <!-- 筛选条件 -->
    <el-card>
      <div slot="header">
        <my-bread>内容管理</my-bread>
      </div>
      <el-form label-width="80px" size="small">
        <el-form-item label="状态：">
          <el-radio-group v-model="reqParams.status">
            <el-radio :label="null">全部</el-radio>
            <el-radio :label="0">草稿</el-radio>
            <el-radio :label="1">待审核</el-radio>
            <el-radio :label="2">审核通过</el-radio>
            <el-radio :label="3">审核失败</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="频道：">
          <el-select v-model="reqParams.channel_id" placeholder="请选择">
            <el-option
              v-for="item in channelOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="日期：">
          <el-date-picker
            v-model="dateArr"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary">筛选</el-button>
        </el-form-item>
      </el-form>
    </el-card>
    <!-- 筛选结果 -->
    <!-- 标签中的内容  称为插槽内容 -->
    <!-- <my-test> -->
    <!-- 作用域插槽 -->
    <!-- slot="content" 指定插槽 -->
    <!-- slot-scope="scope" scope是slot插槽上所有的自定义属性的集合{msg:'组件内部数据'} -->
    <!-- 以前版本写的2.64版本vue -->
    <!-- <div slot="content" slot-scope="scope">content 得到组件内部数据：{{scope.msg}}</div>  -->
    <!-- 目前新版本的写法 -->
    <!-- <template v-slot:content="scope">content 得到组件内部数据：{{scope.msg}}</template> -->
    <!-- v-slot:插槽名称="插槽上所有自定义属性集合数据对象" -->
    <!-- <div slot="footer">footer</div> -->
    <!-- </my-test> -->
  </div>
</template>

<script>
export default {

  data () {
    return {
      // 筛选项表单数据  提交给后台参数
      reqParams: {
        status: null, // 默认选择全部
        channel_id: null
      },
      // 频道下拉选项数据
      channelOptions: [{ value: 1, label: 'js' }], // value label 分别代表值 和说明
      // 日期数据
      dateArr: []
    }
  }

}
</script>

<style scoped lang='less'></style>
