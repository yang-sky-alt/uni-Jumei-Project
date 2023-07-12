<template>
  <view>
    <div class="search-box">
      <!-- <uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar> -->
      <uni-search-bar @input="input" placeholder="请输入关键字" :radius="100" cancelButton="none">
      </uni-search-bar>
    </div>
    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchVal.length !== 0">
      <view class="sugg-item" v-for="(item, i) in searchVal" :key="i" @click="gotoDetail(item.goods_id)">
        <view class="goods-name">{{ item.goods_name }}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item, i) in historys" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      timer: null,
      // 关键词
      kw: '',
      // 搜索的结果列表
      searchVal: "",
      // 搜索关键词的历史记录
      historyList: []
    }
  },
  onLoad() {
    // 初始化获取在持久化存储到本地的数据
    this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
  },

  methods: {
    input(e) {
      clearTimeout(this.timer)
      this.timer = setTimeout(() => {
        this.kw = e
        this.getSearchList()
      }, 500)
    },
    // 根据搜索关键词，搜索商品建议列表
    async getSearchList() {
      // 判断关键词是否为空
      if (this.kw.length === 0) {
        this.searchVal = []
        return
      }
      const { data: result } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw })
      this.searchVal = result.message
      this.saveSearchHistory()
    },
    // 点击跳转
    gotoDetail(goods_id) {
      uni.navigateTo({
        // 指定详情页面的 URL 地址，并传递 goods_id 参数
        url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
      })
    },
    // 保存搜索关键词的方法
    saveSearchHistory() {
      // 直接把搜索关键词 push 到 historyList 数组中
      // this.historyList.push(this.kw)
      // 1 将Array数组转换为Set对象
      const set = new Set(this.historyList)
      // // 2 调用set对象的delete方法 移除对应的元素
      set.delete(this.kw)
      // // 3 调用set的add方法 向set中添加元素
      set.add(this.kw)
      // // 将set对象转换为Array数组
      this.historyList = Array.from(set)
      // 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
      uni.setStorageSync('kw', JSON.stringify(this.historyList))
    },
    // 点击清除搜索记录
    cleanHistory() {
      this.historyList = []
      uni.setStorageSync('kw', '[]')
    },
    // 点击搜索历史跳转到详情页
    gotoGoodsList(item) {
      uni.navigateTo({
        url: '/subpkg/goods_list/goods_list?query=' + item
      })
    }
  },
  computed: {
    historys() {
      // 由于数组是引用类型，所以不要直接基于原数组调用 reverse 方法，以免修改原数组中元素的顺序 而是应该新建一个内存无关的数组，再进行 reverse 反转
      return [...this.historyList].reverse()
    }
  }
}
</script>

<style lang="scss">
.uni-searchbar {
  /* #ifndef APP-NVUE */
  display: flex;
  /* #endif */
  flex-direction: row;
  position: relative;
  padding: 16rpx;
  /* 将默认的 #FFFFFF 改为 #C00000 */
  background-color: #c00000;
}

.search-box {
  position: sticky;
  top: 0;
  z-index: 999;
}

.sugg-list {
  padding: 0 5px;

  .sugg-item {
    font-size: 12px;
    padding: 13px 0;
    border-bottom: 1px solid #efefef;
    display: flex;
    align-items: center;
    justify-content: space-between;

    .goods-name {
      // 文字不允许换行（单行文本）
      white-space: nowrap;
      // 溢出部分隐藏
      overflow: hidden;
      // 文本溢出后，使用 ... 代替
      text-overflow: ellipsis;
      margin-right: 3px;
    }
  }
}

.history-box {
  padding: 0 5px;

  .history-title {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 40px;
    font-size: 13px;
    border-bottom: 1px solid #efefef;
  }

  .history-list {
    display: flex;
    flex-wrap: wrap;

    .uni-tag {
      margin-top: 5px;
      margin-right: 5px;
    }
  }
}
</style>
