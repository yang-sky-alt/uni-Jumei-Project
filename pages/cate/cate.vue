<template>
  <view>
    <!-- 使用自定义的搜索组件 -->
    <view class="search-box">
      <my-search @myclick="gotoSearch"></my-search>
    </view>
    <view class="scroll-view-container">
      <!-- 左侧的滚动视图区域 -->
      <scroll-view class="left-scroll-view" scroll-y :style="{ height: wh + 'px' }">
        <view @click="activeChanged(i)" class="left-scroll-view-item" :class="i === active ? 'active' : ''"
          v-for="(item, i) in cateList" :key="i">
          {{
            item.cat_name }}</view>
      </scroll-view>
      <!-- 右侧的滚动视图区域 -->
      <scroll-view class="right-scroll-view" scroll-y :style="{ height: wh + 'px' }" :scroll-top="scrollTop">
        <view class="cate-lv2" v-for="(item2, i2) in cateLevel2" :key="i2">
          <!-- 二级分类标题 -->
          <view class="cate-lv2-title">/ {{ item2.cat_name }} /</view>
          <!-- 当前二级分类下的三级分类列表 -->
          <view class="cate-lv3-list">
            <!-- 三级分类 Item 项 -->
            <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <!-- 图片 -->
              <image src="/static/tab_icons/home.png"></image>
              <!-- 文本 -->
              <text>{{ item3.cat_name }}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>
</template>

<script>
// 导入自己封装的 mixin 模块
import badgeMix from '@/mixins/tabbar-badge.js'
export default {
  // 将 badgeMix 混入到当前的页面中进行使用
  mixins: [badgeMix],
  onLoad() {
    // 获取当前系统的信息
    const sysInfo = uni.getSystemInfoSync()
    // 为 wh 窗口可用高度动态赋值
    this.wh = sysInfo.windowHeight - 50
    this.getCateList()
  },
  data() {
    return {
      // 窗口的可用高度 = 屏幕高度 - navigationBar高度 - tabBar 高度
      wh: 0,
      // 分类数据列表
      cateList: [],
      active: 0,
      // 二级分类列表
      cateLevel2: [],
      // 滚动条距离顶部的距离
      scrollTop: 0
    };
  },
  methods: {
    async getCateList() {
      const { data: result } = await uni.$http.get('/api/public/v1/categories')
      if (result.meta.status !== 200) return uni.$showMsg()
      this.cateList = result.message
      this.cateLevel2 = result.message[0].children
    },
    activeChanged(i) {
      this.active = i
      this.cateLevel2 = this.cateList[i].children
      this.scrollTop = this.scrollTop === 0 ? 1 : 0
    },
    gotoGoodsList(item) {
      uni.navigateTo({
        url: '/subpkg/goods_list/goods_list?cid=' + item.cat_id
      })
    },
    // 组件自定义事件
    gotoSearch() {
      uni.navigateTo({
        url: '/subpkg/search_page/search_page'
      })
    }
  }
}
</script>

<style lang="scss">
.scroll-view-container {
  display: flex;

  .left-scroll-view {
    width: 120px;

    .left-scroll-view-item {
      line-height: 60px;
      background-color: #f7f7f7;
      text-align: center;
      font-size: 12px;

      /* 激活项的样式 */
      &.active {
        background-color: #ffffff;
        position: relative;

        /* 渲染激活项左侧的红色指示边线 */
        &::before {
          content: ' ';
          display: block;
          width: 3px;
          height: 30px;
          background-color: #c00000;
          position: absolute;
          left: 0;
          top: 50%;
          transform: translateY(-50%);
        }
      }
    }
  }
}

.cate-lv2-title {
  font-size: 12px;
  font-weight: bold;
  text-align: center;
  padding: 15px 0;

}

.cate-lv3-list {
  display: flex;
  flex-wrap: wrap;

  .cate-lv3-item {
    width: 33.33%;
    margin-bottom: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;

    image {
      width: 60px;
      height: 60px;
    }

    text {
      font-size: 12px;
    }
  }
}

.search-box {
  /* 设置定位效果为“吸顶” */
  position: sticky;
  /* 吸顶的“位置” */
  top: 0;
  /* 提高层级，防止被轮播图覆盖 */
  z-index: 999;
}
</style>
