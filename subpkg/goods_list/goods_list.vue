<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(goods)">
        <my-goods :goods="goods"></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // 请求参数对象
      queryObj: {
        // 查询关键词
        query: '',
        // 商品分类Id
        cid: '',
        // 页码值
        pagenum: 1,
        // 每页显示多少条数据
        pagesize: 10
      },
      // 商品列表的数据
      goodsList: [],
      // 总数量，用来实现分页
      total: 0,
      // 是否正在请求数据
      isloading: false
    };
  },
  onLoad(options) {
    // 将页面参数转存到 this.queryObj 对象中
    this.queryObj.query = options.query || ''
    this.queryObj.cid = options.cid || ''
    // 调用获取商品列表数据的方法
    this.getGoodsList()
  },
  methods: {
    // 获取商品列表数据的方法
    async getGoodsList(cb) {
      // 打开节流阀
      this.isloading = true
      // 发起请求
      const { data: result } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
      //  关闭节流阀
      this.isloading = false
      // 只要数据请求完毕，就立即按需调用 cb 回调函数
      cb && cb()
      if (result.meta.status !== 200) return uni.$showMsg()
      // 为数据赋值：通过展开运算符的形式，进行新旧数据的拼接
      this.goodsList = [...this.goodsList, ...result.message.goods]
      this.total = result.message.total
    },
    // 跳转去详情
    gotoDetail(goods) {
      uni.navigateTo({
        url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
      })
    }
  },
  // 触底的事件
  onReachBottom() {
    // 判断是否还有下一页 当前的页码值 * 每页显示多少条数据 >= 总数条数 
    if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')

    // 判断是否正在请求其它数据，如果是，则不发起额外的请求
    if (this.isloading) return
    // 页码值加一
    this.queryObj.pagenum++
    this.getGoodsList()
  },
  // 下拉刷新的事件
  onPullDownRefresh() {
    // 1. 重置关键数据
    this.queryObj.pagenum = 1
    this.total = 0
    this.isloading = false
    this.goodsList = []

    // 2. 重新发起请求
    this.getGoodsList(() => uni.stopPullDownRefresh())
  },

}
</script>

<style lang="scss"></style>
