<template>
  <!-- 最外层的容器 -->
  <view class="my-settle-container">
    <!-- 全选区域 -->
    <label class="radio" @click="changeAllState">
      <radio color="#C00000" :checked="isFullCheck" /><text>全选</text>
    </label>

    <!-- 合计区域 -->
    <view class="amount-box">
      合计:<text class="amount">￥{{ checkedGoodsAmount }}</text>
    </view>
    <!-- 结算按钮 -->
    <view class="btn-settle" @click="settlement">结算({{ checkedCount }})</view>
  </view>
</template>

<script>
import { mapGetters, mapMutations, mapState } from 'vuex'
export default {
  name: "my-settle",
  data() {
    return {

    };
  },
  methods: {
    ...mapMutations('m_cart', ['updateAllGoodsState']),
    // 反选/全选
    changeAllState() {
      this.updateAllGoodsState(!this.isFullCheck)
    },
    // 点击了结算按钮
    settlement() {
      // 判断是否选中了需要购买的商品
      if (!this.checkedCount) return uni.$showMsg('请选择要结算的商品！')
      // 判断用户是否选中了收货地址
      if (!this.addstr) return uni.$showMsg('请添加收货地址！')
      // 判断是否登录
      if (!this.userInfo) return uni.$showMsg('请先登录！')
    }
  },
  computed: {
    ...mapGetters('m_cart', ['checkedCount', 'total', "checkedGoodsAmount"]),
    ...mapGetters('m_user', ['addstr']),
    ...mapState('m_user', ['userinfo']),
    // 2. 是否全选
    isFullCheck() {
      return this.total === this.checkedCount
    },
  }
}
</script>

<style lang="stylus">
.my-settle-container {
  /* 底部固定定位 */
  position: fixed;
  bottom: 0;
  left: 0;
  /* 设置宽高和背景色 */
  width: 100%;
  height: 50px;
   // 将背景色从 cyan 改为 white
   background-color: white;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-left: 5px;
  font-size: 14px;
  .radio {
    display: flex;
    align-items: center;
  }
  .amount {
    color: #c00000;
  }
  .btn-settle {
    height: 50px;
    min-width: 100px;
    background-color: #c00000;
    color: white;
    line-height: 50px;
    text-align: center;
    padding: 0 10px;
  }
}
</style>