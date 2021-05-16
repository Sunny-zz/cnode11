<template>
  <div :style="{ width: formatWidth }" class="panel">
    <div class="header">
      <slot name="header" />
    </div>
    <div class="content">
      <slot name="content" />
    </div>
  </div>
</template>

<script>
// 这是一个布局组件 panel
export default {
  props: {
    width: {
      type: [String, Number],
      default: "100%",
    },
  },
  computed: {
    formatWidth() {
      const { width } = this;
      // '100'   ----> '100px'
      // 100   ---->  '100px'
      // '100%' ----> '100%'
      // 先判断时候是数字 是的话直接变成加 px 的字符串 不是的话看是否可以使用 Number 转化成数字 能的话变成加 px 的字符串 不能就不修改了
      // return typeof width === "number" ? width + "px" : Number(width) ? width + 'px' : width
      return typeof width === "number" || Number(width) ? width + "px" : width;
    },
  },
};
</script>

<style lang='less'>
.panel {
  width: 100%;
  border-radius: 4px;
  margin-top: 10px;
  .header {
    padding: 10px;
    background-color: #f6f6f6;
  }
  .content {
    padding: 10px;
    background-color: #fff;
  }
}
</style>