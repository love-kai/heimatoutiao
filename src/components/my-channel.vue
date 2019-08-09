<template>
  <!--  -->
  <el-select clearable :value="value" @change="fn" placeholder="请选择">
    <el-option v-for="item in channelOptions" :key="item.id" :label="item.name" :value="item.id"></el-option>
  </el-select>
</template>

<script>
// computed 计算属性使用场景：当你需要一个新数据，需要依赖data中的数据。
// watch 侦听器的使用场景：当你需要监听某一个属性的变化，去做一些开销较大操作(异步操作),当然单纯的其他操作也行
// watch: {
//   //  监听id值为对应一个函数
//   'reqParams.channel_id': function (newVal, oldVal) {
//     if (newVal === '') {
//       this.reqParams.channel_id = null
//     }
//   }
// },
// 注意，当你清空内容的时候，值为空字符，处理为空字符
export default {
  // 这个文件是频道的总文件
  // 没有的组件内容要声明一个数据
  // 如果没有声明name属性控制台会报错
  name: 'my-channel',
  props: ['value'], // 仅仅读取数据
  data () {
    return {
      // myValue: null,
      channelOptions: []
    }
  },
  // 钩子函数created
  created () {
    // 下载频道下拉选项数据
    this.getChannelOptions()
  },
  methods: {
    //   频道组件的相关数据
    async getChannelOptions () {
      //  第一个data为响应主体 后面的data为具体的数据
      const { data: { data } } = await this.$http.get('channels')
      this.channelOptions = data.channels
    },
    fn (val) {
      // 字符为空字符处理为null
      if (val === '') val = null
      // 数据提交给父组件使用(子传父)
      this.$emit('input', val)
    }
  }
}
</script>

<style scoped lang='less'>
</style>
