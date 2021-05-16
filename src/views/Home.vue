<template>
  <div class="home">
    <Panel :width="'80%'">
      <template #header>
        <div class="nav">
          <router-link to="/all">首页</router-link>
          <router-link to="/good">精华</router-link>
          <router-link to="/share">分享</router-link>
          <router-link to="/ask">问答</router-link>
          <router-link to="/job">招聘</router-link>
          <router-link to="/dev">客户端测试</router-link>
        </div>
      </template>
      <template #content>
        <div v-if="posts.length">
          <div class="post-item" v-for="post in posts" :key="post.id">
            <router-link :to='`/user/${post.author.loginname}`'>
              <img
                class="avatar"
                :title="post.author.loginname"
                :src="post.author.avatar_url"
                alt=""
              />
            </router-link>
            <span
              ><span>{{ post.reply_count }}</span
              >/<span>{{ post.visit_count }}</span></span
            >
            <!-- 当处于首页 或者 文章是置顶的 或者精华的 类别展示会出现-->
            <span
              v-if="!tab || tab === 'all' || post.top || post.good"
              :class="post.top || post.good ? 'active-tab' : 'tab'"
              >{{ post | getPostChineseTab }}</span
            >
            <router-link
              class="title"
              :to="{ name: 'topic', params: { topicId: post.id } }"
              >{{ post.title }}</router-link
            >
          </div>
          <!-- 比如说点击了 分享的第三页    /share?page=3 -->
          <!-- 获取当前页面的地址 -->
          <div>
            我是假的分页器
            <button>
              <router-link :to="$route.path + '?page=3'">3</router-link>
            </button>
            <button>
              <router-link :to="$route.path + '?page=4'">4</router-link>
            </button>
            <button @click="pageClick(5)">5</button>
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
  name: "Home",
  data() {
    return {
      posts: [],
      pageTotalList: [
        {
          tab: "dev",
          total: 320,
        },
        {
          tab: "ask",
          total: 260,
        },
      ],
    };
  },
  // 通过监听动态路由参数 获取对应的分类数据
  watch: {
    tab: {
      async handler(newValue) {
        // console.log(newValue);
        // const tab = newValue || 'all'
        const queryTab = newValue ? `&tab=${newValue}` : "";
        const res = await this.$axios.get(`/topics?page=1&limit=40${queryTab}`);
        this.posts = res.data;
      },
      immediate: true,
    },
  },
  computed: {
    tab() {
      console.log(this.$route);
      return this.$route.params.tab;
    },
  },
  methods: {
    pageClick(page) {
      this.$router.push(this.$route.path + "?page=" + page);
    },
  },
};
</script>
<style lang="less">
.nav a {
  margin-right: 10px;
}
.post-item {
  .avatar {
    width: 40px;
    border-radius: 4px;
    height: 40px;
    vertical-align: middle;
    margin: 4px 0;
  }
  .title {
    width: 65%;
    display: inline-block;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .tab {
    background-color: #e5e5e5;
    color: #999;
    padding: 2px 4px;
    border-radius: 3px;
    font-size: 12px;
  }
  .active-tab {
    background: #80bd01;
    padding: 2px 4px;
    border-radius: 3px;
    color: #fff;
    font-size: 12px;
  }
}
</style>