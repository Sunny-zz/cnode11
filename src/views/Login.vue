<template>
  <div class="loginin">
    <input v-model.trim="token" type="text" />
    <button @click="login">登录</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // ecf878d1-6052-476a-8262-824760c7872b
      token: "ecf878d1-6052-476a-8262-824760c7872b",
    };
  },
  methods: {
    login() {
      this.$axios.post("/accesstoken", {
        accesstoken: this.token,
      }).then(res => {
        // console.log(res)
        this.$router.push('/')
        const userInfo = res
        delete userInfo.success
        this.$store.commit('getUserInfo', userInfo)
      }).catch(err => {
        console.log(err)
        alert('token 有误')
      })
    },
  },
};
</script>

<style>
</style>