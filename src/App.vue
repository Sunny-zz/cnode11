<template>
  <div class="app">
    <Header />
    <div class="main">
      <router-view class="left" />
      <!-- 右侧小信息模块 -->
      <Panel :width="200" v-if="!$route.path.includes('login')" class="right">
        <template #header>
          <span v-if="userInfo">个人信息</span>
          <span v-else>网站信息</span>
        </template>
        <template #content>
          <div v-if='userInfo'>
            <div>
              <img :src="userInfo.avatar_url" alt="">
              <span>{{userInfo.loginname}}</span>
            </div>
          </div>
          <div v-else>
            <p>Node.js专业中文社区</p>
            <router-link to='/login'>登录</router-link>
          </div>
        </template>
      </Panel>
    </div>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import Panel from "./layout/Panel.vue";
export default {
  components: { Header, Panel },
  computed: {
    // 刷新页面的时候 store 数据清空
    // 如何处理
    // 当第一次登陆的时候可以直接将 userInfo 转化成 json 串存储到浏览器中
    // created 先看能不能取到 vuex 数据不能的话直接去浏览器中取 并更新 store
    // 当点击退出按钮的时候清空 store 以及浏览器存储的数据
    userInfo() {
      return this.$store.state.userInfo 
    }
  },
};
</script>
<style lang='less'>
// @import url('./theme/style.less');
// .app {
//   h1 {
//     color: @main-color;
//   }
// }
.main {
  display: flex;
  .left {
    width: 80%;
    margin: 20px  50px;
  }
  .right {
    margin-top: 20px;
    margin-right: 50px;
  }
}
</style>
