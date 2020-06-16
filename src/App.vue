<template>
  <div id="app">
    <div class="header">header</div>
    <VList class="list" :list="list" direction="y" :get-size="getSize">
      <template v-slot:vlist="{ vlist }">
        <Item v-for="item in vlist" :key="item.id" :item="item"></Item>
      </template>
    </VList>
    <div class="footer">footer</div>
  </div>
</template>

<script>
import VList from './components/vlist';
import Item from './components/listItem';

export default {
  data() {
    const list = new Array(1000).fill(0).map((_, i) => {
      return {
        id: i + 1,
        title: `${i + 1}. 小黄鸭修复水`,
        img: 'https://wx1.sinaimg.cn/mw690/696bad14gy1gf4u27tv5dj21tn1tn1kx.jpg',
        price: (Math.random() * 100).toFixed(2),
      };
    });
    return {
      list,
    };
  },
  methods: {
    getSize(item) {
      return [375, 100];
    },
  },
  components: {
    VList,
    Item,
  },
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
}
#app {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  display: flex;
  flex-direction: column;
}
.header, .footer {
  font-size: 18px;
  text-align: center;
  line-height: 44px;
  background: #f3f3f3;
}
.list {
  flex: 1 1;
}
</style>
