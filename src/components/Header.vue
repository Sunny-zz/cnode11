<template>
  <header>
    <router-link class="logo" to='/'><img alt="Vue logo" src="../assets/logo.png"></router-link>
    <router-link class="info" to='/'>未读消息</router-link>
    <span class="signout">退出</span>
  </header>
</template>

<script>
export default {
  name: 'HeaderComp'
}
</script>

<style lang='less'>
header {
  width: 100%;
  height: 50px;
  background-color: #444;
  line-height: 50px;
  color: aliceblue;
  .logo{
    img{
      width: 45px;
    }
  }
  a.info{
    color: aliceblue;
    margin-right: 40px;
  }
  span.signout{
    cursor: pointer;
  }
}
</style>
