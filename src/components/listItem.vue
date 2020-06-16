<template>
  <div class="item">
    <div>{{ item.title }}</div>
    <div>￥{{ item.price }}</div>
    <div>{{ Math.random() }}</div>
  </div>
</template>

<script>
export default {
  props: {
    item: {
      required: true,
    },
  },
};
</script>

<style scoped>
.item {
  height: 100px;
  box-sizing: border-box;
  border-bottom: solid 1px #eaeaea;
}
</style>
