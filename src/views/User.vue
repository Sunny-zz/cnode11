<template>
  <div v-if="user">
    <Panel>
      <template #header>
        <router-link to="/">主页</router-link>/
        <div>
          <img :src="user.avatar_url" alt="" />
          <div>{{ user.loginname }}</div>
          <div>发布于 {{ user.create_at }}</div>
        </div>
      </template>
    </Panel>
    <Panel>
      <template #header>
        <span>最近创建的话题</span>
        <div>
          <div v-for="item in user.recent_topics.slice(0, 5)" :key="item.id">
            <router-link :to="`/user/${item.author.loginname}`"
              ><img width="30" :src="item.author.avatar_url" alt=""
            /></router-link>
            <router-link :to="`/topic/${item.id}`">{{
              item.title
            }}</router-link>
          </div>
        </div>
      </template>
    </Panel>
    <Panel>
      <template #header>
        <span>最近参与的话题</span>
        <div>
          <div v-for="item in user.recent_replies" :key="item.id">
            <router-link :to="`/user/${item.author.loginname}`"
              ><img width="30" :src="item.author.avatar_url" alt=""
            /></router-link>
            <router-link :to="`/topic/${item.id}`">{{
              item.title
            }}</router-link>
          </div>
        </div>
      </template>
    </Panel>
  </div>
</template>

<script>
import Panel from "../layout/Panel.vue";
export default {
  components: { Panel },
  data() {
    return {
      user: null,
    };
  },
  watch: {
    "$route.params.loginname": {
      async handler() {
        // console.log(newValue);
        const { loginname } = this.$route.params;
        const res = await this.$axios.get(`/user/${loginname}`);
        this.user = res.data;
      },
      immediate: true,
    },
  },
};
</script>

<style>
</style>