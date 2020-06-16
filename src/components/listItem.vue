<template>
  <div class="item">
    <img class="img" :src="item.img">
    <div class="title">{{ item.title }}</div>
    <div class="price">￥{{ item.price }}</div>
    <div class="random">{{ Math.random() }}</div>
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
  position: relative;
  padding-left: 100px;
}
.img {
  position: absolute;
  left: 12px;
  top: 12px;
  width: 76px;
  height: 76px;
  object-fit: cover;
}
.title {
  font-size: 16px;
  padding-top: 12px;
  margin-bottom: 8px;
}
.price {
  font-size: 14px;
  margin-bottom: 8px;
  color: #666;
}
.random {
  font-size: 12px;
  color: #666;
}
</style>
