<template>
  <div class="contain">
    <!-- 头部组件 -->
    <top />
    <!-- 主题组件 -->
    <index></index>
  </div>
</template>

<script>
import top from "./components/01.top.vue";
import index from "./components/02.index.vue";
export default {
  name: "App",
  components: {
    top,
    index,
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
.contain {
  margin-bottom: 60px;
}
</style>
