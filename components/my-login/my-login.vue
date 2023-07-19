<template>
  <view class="login-container">
    <!-- 提示登录的图标 -->
    <uni-icons type="contact-filled" size="100" color="#AFAFAF"></uni-icons>
    <!-- 登录按钮 -->
    <button type="primary" class="btn-login" open-type="getUserInfo" @getuserinfo="getUserInfo">一键登录</button>
    <!-- 登录提示 -->
    <view class="tips-text">登录后尽享更多权益</view>
  </view>
</template>

<script>
import { mapMutations } from 'vuex'
export default {
  name: "my-login",
  data() {
    return {

    };
  },
  methods: {
    ...mapMutations('m_user', ['updateUserInfo']),
    // 获取用户基本信息
    getUserInfo(e) {
      // 判断是否获取用户信息成功
      if (e.detail.errMsg === 'getUserInfo:fail auth deny') return uni.$showMsg('您取消了登录授权！')
      this.updateUserInfo(e.detail.userInfo)
      // 获取成功后的登录字符串
      this.getToken(e.detail)
    },
    // 调用登录接口 换取永久的token
    async getToken(info) {
      const [err, result] = await uni.login().catch(err => err)
      // 判断是否 uni.login() 调用失败
      if (err || result.errMsg !== 'login:ok') return uni.$showError('登录失败！')

    }
  }
}
</script>

<style lang="scss">
.login-container {
  // 登录盒子的样式
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #f8f8f8;
  position: relative;
  overflow: hidden;

  // 绘制登录盒子底部的半椭圆造型
  &::after {
    content: ' ';
    display: block;
    position: absolute;
    width: 100%;
    height: 40px;
    left: 0;
    bottom: 0;
    background-color: white;
    border-radius: 100%;
    transform: translateY(50%);
  }

  // 登录按钮的样式
  .btn-login {
    width: 90%;
    border-radius: 100px;
    margin: 15px 0;
    background-color: #c00000;
  }

  // 按钮下方提示消息的样式
  .tips-text {
    font-size: 12px;
    color: gray;
  }
}
</style>