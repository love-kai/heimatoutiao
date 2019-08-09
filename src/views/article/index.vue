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
          <!-- 使用自己的组件 -->
          <!-- V-model 背后：value @input -->
          <my-channel v-model="reqParams.channel_id"></my-channel>
        </el-form-item>
        <el-form-item label="日期：">
          <!--  value-format="yyyy-MM-dd" 设置日期格式 -->
          <el-date-picker
            value-format="yyyy-MM-dd"
            v-model="dateArr"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            @change="changeDate"
          ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="search()">筛选</el-button>
        </el-form-item>
      </el-form>
      <!-- 筛选结果 -->
    </el-card>
    <el-card>
      <div slot="header">根据筛选条件共查询到 {{total}} 条结果</div>
      <!-- 表格组件 -->
      <el-table :data="articles">
        <el-table-column label="封面">
          <template slot-scope="scope">
            <!-- fit 平铺的方式 -->
            <el-image :src="scope.row.cover.images[0]" fit="cover" style="width:120px;height:80px">
              <div slot="error" class="image-slot">
                <img src="../../assets/images/error.gif" style="width:120px;height:80px" alt />
              </div>
            </el-image>
            <!-- {{scope.row}} -->
          </template>
        </el-table-column>
        <el-table-column label="标题" prop="title"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status===0" type="info">草稿</el-tag>
            <el-tag v-if="scope.row.status===1">待审核</el-tag>
            <el-tag v-if="scope.row.status===2" type="success">审核通过</el-tag>
            <el-tag v-if="scope.row.status===3" type="warning">审核失败</el-tag>
            <el-tag v-if="scope.row.status===4" type="danger">删除</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="发布时间" prop="pubdate"></el-table-column>
        <el-table-column label="操作" width="120px">
          <template slot-scope="scope">
            <el-button plain type="primary" @click="edit(scope.row.id)" icon="el-icon-edit" circle></el-button>
            <el-button plain type="danger" @click="del(scope.row.id)" icon="el-icon-delete" circle></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页组件 -->
      <div style="text-align:center ;margin-top:30px">
        <!-- :total="1000" 指定总条数 -->
        <!-- 默认一页显示10条 :page-size="reqParams.per_page" -->
        <!-- @current-change="changePager"  页码改变事件 -->
        <!-- 更新过数据后  当前页码也需要修改  选中对应的按钮 current-page -->
        <el-pagination
          background
          layout="prev, pager, next"
          :total="total"
          :page-size="reqParams.per_page"
          :current-page="reqParams.page"
          @current-change="changePager"
        ></el-pagination>
        <!-- changePager 带括号为自己传参  不带括号接收默认传参  -->
      </div>
    </el-card>
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
      // axios 提交的数据 值为Null是不会携带参数
      reqParams: {
        status: null, // 默认选择全部
        channel_id: null, // 给数据赋值
        begin_pubdata: null,
        end_pubdata: null,
        page: 1,
        per_page: 20

      },
      // 频道下拉选项数据
      channelOptions: [], // value label 分别代表值 和说明
      // 日期数据
      dateArr: [],
      // 文章列表
      articles: [],
      // 总条数
      total: 0
    }
  },
  created () {
    // 获取文章列表数据
    this.getArticles()
  },
  methods: {
    // 编辑函数
    edit (id) {
      this.$router.push('/publish?id=' + id)
    },
    // 删除函数
    del (id) {
      this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        // 点击确认
        await this.$http.delete(`articles/${id}`)
        // 这两句代码没有执行 上面一句代码报错 没有抛出错误
        this.$message.success('删除文章成功')
        this.getArticles()// 跟新列表
      }).catch(() => {
      })
    },
    // 日期选择后的事件
    changeDate (dateArr) {
      // 严谨一些，清空数据考虑在内
      if (dateArr) {
        this.reqParams.begin_pubdate = dateArr[0]
        this.reqParams.end_pubdate = dateArr[1]
      } else {
        this.reqParams.begin_pubdate = null
        this.reqParams.end_pubdate = null
      }
    },
    // 筛选函数
    search () {
      // 筛选项双向绑定  拿着对应的数据发请求即可  注意：重新筛选后页码第一页
      this.reqParams.page = 1 // 修改当前页面为第一页
      this.getArticles()
    },
    // 改变分页事件对应的函数
    changePager (newPage) {
      // 修改获取数据的页码
      this.reqParams.page = newPage
      this.getArticles()
    },

    //  获取数据函数
    async getArticles () {
      // axios get传参  url?key=value&key1=value1 ... 麻烦
      // axios get传参  第二传参是对象 {params:指定传参对象}  发请求的时候自动拼接地址栏后  this.reqParams请求传参
      const { data: { data } } = await this.$http.get('articles', { params: this.reqParams })
      // 列表数据
      this.articles = data.results
      // 总条数
      this.total = data.total_count
    }
  }

}
</script>

<style scoped lang='less'>
.el-card {
  margin-bottom: 30px;
}
</style>
