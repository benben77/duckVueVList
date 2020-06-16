<template>
  <div id="app">
    <div class="header">header</div>
    <VList class="list" :list="list" direction="y" :get-size="getSize" @loadMore="onLoadMore">
      <template v-slot:vlist="{ vlist }">
        <Item v-for="item in vlist" :key="item.id" :item="item"></Item>
      </template>
    </VList>
    <div class="footer">footer</div>
    <div v-if="loading" class="loading">loading</div>
  </div>
</template>

<script>
import VList from './components/vlist';
import Item from './components/listItem';

const getItems = function(len) {
  return new Promise(r => {
    setTimeout(() => {
      r(new Array(100).fill(0).map((_, i) => {
        const id = i + 1 + len;
        return {
          id,
          title: `${id}. 小黄鸭修复水`,
          img: 'https://wx1.sinaimg.cn/mw690/696bad14gy1gf4u27tv5dj21tn1tn1kx.jpg',
          price: (Math.random() * 100).toFixed(2),
        };
      }));
    }, 1000);
  });
};

export default {
  data() {
    return {
      loading: false,
      hasMore: true,
      list: [],
    };
  },
  methods: {
    getSize(item) {
      return [375, 100];
    },
    async onLoadMore() {
      if (this.loading || !this.hasMore) return;
      this.loading = true;
      const { list } = this;
      const data = await getItems(list.length);
      this.loading = false;
      list.push(...data);
      if (list.length < 1000) {
        this.hasMore = true;
      } else {
        this.hasMore = false;
      }
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

.loading {
  position: fixed;
  left: 50%;
  top: 50%;
  margin-top: -60px;
  margin-left: -60px;
  height: 120px;
  line-height: 120px;
  width: 120px;
  border-radius: 8px;
  font-size: 24px;
  text-align: center;
  background: rgba(0, 0, 0, .3);
  color: #fff;
}
</style>
