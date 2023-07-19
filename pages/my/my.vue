<template>
  <view class="my-container">
    <my-login v-if="!userinfo"></my-login>
    <my-userinfo v-else></my-userinfo>
  </view>
</template>

<script>
import { mapState } from 'vuex'
// 导入自己封装的 mixin 模块
import badgeMix from '@/mixins/tabbar-badge.js'
export default {
  // 将 badgeMix 混入到当前的页面中进行使用
  mixins: [badgeMix],
  data() {
    return {

    }
  },
  methods: {

  },
  computed: {
    ...mapState('m_user', ['userinfo'])
  }
}
</script>

<style>
page,
.my-container {
  height: 100%;
}
</style>
