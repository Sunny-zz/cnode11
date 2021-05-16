<template>
  <div v-if="topic">
    <Panel :width="600">
      <template #content>
        <div>
          <h2>
            {{ topic.title }}
            <!-- 需要登录了才出现这个按钮 -->
            <button v-if="true" @click="collect">
              {{ topic.is_collect ? "取消收藏" : "收藏" }}
            </button>
          </h2>
          <div v-html="topic.content"></div>
        </div>
      </template>
    </Panel>
    <Panel>
      <template #header>
        <span>回复列表</span>
      </template>
      <template #content>
        <div>
          <div v-for="comment in topic.replies" :key="comment.id">
            <img width="30px" :src="comment.author.avatar_url" alt="" />
            <span>{{ comment.author.loginname }}</span>
            <span v-html="comment.content"></span>
            <div>
              <!-- 没登录 如果没有点赞数不显示点赞按钮 -->
              <!-- 登录了 没有点赞数显示点赞按钮不显示点赞个数0 -->
              <button
                @click="upClick(comment.id)"
                :class="{ 'up-active': comment.is_uped }"
              >
                点赞{{ comment.ups.length }}
              </button>
              <button v-if="isLogin" @click="otherSubmit(comment)">回复</button>
            </div>
            <div v-if="comment.id === reply_id">
              <textarea ref="textarea" v-model="reply_text"></textarea>
              <button @click="otherSubmit1(comment.id)">回复</button>
            </div>
            <hr />
          </div>
        </div>
        <br />
        <br />
        <div>
          <textarea v-model.trim="text"></textarea>
          <button @click="submit">回复</button>
        </div>
      </template>
    </Panel>
  </div>
</template>

<script>
// 文章详情页 登录不登录是展示不同的内容
// vuex 里面又能存储了登录状态，但是这个登录状态必须刷新的时候也会自动更新
// 如果 vuex 内更新登录状态 需要发请求的话，在该组件中不一定能获取到最新的登录状态
// 取 token， 前提是登录的时候将 token 存储到了浏览器
import Panel from "../layout/Panel.vue";
// ecf878d1-6052-476a-8262-824760c7872b

export default {
  components: { Panel },
  name: "Topic",
  data() {
    return {
      topic: null,
      // text 是回复文章的评论内容
      text: "",
      // 回复的评论 id
      reply_id: "",
      // 回复评论的评论内容
      reply_text: "",
    };
  },
  computed: {
    isLogin() {
      return true;
    },
  },
  async created() {
    const { topicId } = this.$route.params;
    // 需要判断登录了吗
    const token = true;
    const url = token
      ? `/topic/${topicId}?accesstoken=ecf878d1-6052-476a-8262-824760c7872b`
      : `/topic/${topicId}`;
    const res = await this.$axios.get(url);
    // console.log(res.data);
    this.topic = res.data;
  },
  methods: {
    // 专门请求文章数据函数
    getTopic() {},
    // 点赞和取消点赞
    async upClick(id) {
      // console.log(id);
      // 点赞之前要判断 登没登录
      // 还要判断是不是自己的评论  评论的作者的 loginname 是不是自己
      if (true) {
        const res = await this.$axios.post(`/reply/${id}/ups`, {
          accesstoken: "ecf878d1-6052-476a-8262-824760c7872b",
        });
        // console.log(res);
        // 如何更新页面   点赞变色  个数加一
        // 方案1 根据请求的结果 up  or  down 去更新对应评论的 is_uped 以及  ups  ,更新 ups 需要用户的 id   "59a6b400d97b7e23082428d0"
        // 方案2 重新请求一遍    得看实际情况 需要网络请求
        const userId = "59a6b400d97b7e23082428d0";
        const currentComment = this.topic.replies.find(
          (item) => item.id === id
        );
        currentComment.is_uped = res.action === "up" ? true : false;
        res.action === "up"
          ? currentComment.ups.push(userId)
          : currentComment.ups.splice(
              currentComment.ups.indexOf("59a6b400d97b7e23082428d0"),
              1
            );
      }
    },
    // 文章的回复
    async submit() {
      const { text } = this;
      if (text) {
        // 执行添加评论操作  post /topic/:topic_id/replies 新建评论
        const res = await this.$axios.post(`/topic/${this.topic.id}/replies`, {
          accesstoken: "ecf878d1-6052-476a-8262-824760c7872b",
          content: text,
        });
        // 如何更新页面
        // 1. 根据后台 id  和 text 作假的评论对象 更新 topic
        // 2. 重新请求
        const newComment = {
          id: res.reply_id,
          is_uped: false,
          ups: [],
          content: `<p>${text}<p>`,
          author: {
            avatar_url:
              "https://avatars.githubusercontent.com/u/31389080?v=4&s=120",
            loginname: "sunny-zz",
          },
        };
        this.topic.replies.push(newComment);
        this.text = "";
      }
    },
    otherSubmit(comment) {
      // 在这其实要让对应评论的富文本框出现
      // 在这我们不写对应的富文本框了，直接用下面的 textarea
      const { id, author } = comment;
      this.reply_id = id;
      this.reply_text = `@${author.loginname} `;
      // 更新完 data 之后马上获取更新之后的 dom 是获取不到的
      this.$nextTick(function () {
        this.$refs.textarea[0].focus();
      });
    },
    async otherSubmit1(id) {
      const { reply_text } = this;
      const res = await this.$axios.post(`/topic/${this.topic.id}/replies`, {
        accesstoken: "ecf878d1-6052-476a-8262-824760c7872b",
        content: reply_text,
        reply_id: id,
      });
      // 如何更新页面
      // 1. 根据后台 id  和 text 作假的评论对象 更新 topic
      // 2. 重新请求
      const newComment = {
        id: res.reply_id,
        is_uped: false,
        ups: [],
        content: `<p>${reply_text.replace(
          /@[\w-]+ /,
          `<a href='/user/xxx'>@xxx </a>`
        )}<p>`,
        author: {
          avatar_url:
            "https://avatars.githubusercontent.com/u/31389080?v=4&s=120",
          loginname: "sunny-zz",
        },
      };
      this.topic.replies.push(newComment);
      this.reply_text = "";
      this.reply_id = "";
    },
    // 收藏事件
    async collect() {
      const { is_collect, id } = this.topic;
      console.log(is_collect, id)
      const token = "ecf878d1-6052-476a-8262-824760c7872b";
      if (is_collect) {
        // 取消收藏请求
        await this.$axios.post("/topic_collect/de_collect", {
          topic_id: id,
          accesstoken: token,
        });
        this.topic.is_collect = false;
      } else {
        // 收藏请求
        await this.$axios.post("/topic_collect/collect", {
          topic_id: id,
          accesstoken: token,
        });
        this.topic.is_collect = true;
      }
    },
  },
};
</script>

<style>
.markdown-text p img {
  width: 100%;
}
.up-active {
  color: red;
}
</style>