<template>
  <div class="container">
    <div class="header">header</div>
    <div class="content">
      <slot name="content" :msg="msg">默认内容</slot>
      <!-- 默认插槽 -->
    </div>
    <div class="footer">
      <!-- 默认插槽 -->
      <!-- 具名插槽 添加name值 -->
      <slot name="footer">默认底部</slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'my-test',
  data () {
    return {
      msg: 'my-test的数据'
    }
  }

}
</script>

<style scoped lang='less'>
</style>
