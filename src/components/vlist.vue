<template>
  <div ref="list" class="vlist" :class="listCls" @scroll="onScroll">
    <div :style="{ height: `${paddingTop}px` }"></div>
    <slot name="vlist" v-bind:vlist="vlist"></slot>
    <div :style="{ height: `${paddingBottom}px` }"></div>
  </div>
</template>

<script>
const throttle = function(cb, time) {
  let id = null;
  return function() {
    if (id) return;
    id = setTimeout(function() {
      cb();
      id = null;
    }, time);
  };
};

export default {
  props: {
    list: {
      type: Array,
      required: true,
    },
    margin: {
      type: Number,
      default: 0,
    },
    loadMoreOffset: {
      type: Number,
      default: 10,
    },
    getSize: {
      type: Function,
      required: true,
    },
    direction: {
      default: 'y',
    },
  },
  data() {
    this.list.addItems = this.addItems; // TODO: 用事件订阅更好一些
    return {
      w: 0,
      h: 0,
      scrollPos: 0,
      vlist: [],
      paddingTop: 0,
      paddingBottom: 0,
    };
  },
  computed: {
    size() {
      return this.direction === 'y' ? height : width;
    },
  },
  created() {
    this.updateOffsetList([0], this.list);
  },
  mounted() {
    this.onResize();
    this.render = throttle(this.doRender, 300);
    window.addEventListener('resize', this.onResize);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.onResize);
  },
  computed: {
    listCls() {
      return [this.direction === 'y' ? 'vlist--y' : 'vlist--x'];
    },
  },
  methods: {
    addItems(...items) {
      this.list.push(...items);
      this.updateOffsetList(this.offsetList, items);
      this.doRender();
    },
    onResize() {
      const { width, height } = this.$el.getClientRects()[0];
      this.w = width;
      this.h = height;
      const { scrollTop, scrollLeft } = this.$refs.list;
      this.scrollPos = this.direction === 'y' ? scrollTop : scrollLeft;
      this.doRender();
    },
    updateOffsetList(oldOffsetList, list) {
      const offsetList = oldOffsetList.slice();
      const { getSize } = this;
      let lastOffset = offsetList[offsetList.length - 1];
      list.forEach(x => {
        const [w, h] = getSize(x);
        lastOffset += h;
        offsetList.push(lastOffset);
      });
      this.offsetList = offsetList;
    },
    onScroll() {
      const { scrollTop, scrollLeft } = this.$refs.list;
      this.scrollPos = this.direction === 'y' ? scrollTop : scrollLeft;
      this.render();
    },
    doRender() {
      const { offsetList, h, loadMoreOffset } = this;
      let { margin } = this;
      if (margin <= 0) margin = h;
      const start = Math.max(this.scrollPos - margin, 0);
      const len = this.list.length;
      const end = Math.min(this.scrollPos + h + margin, offsetList[len]);
      let paddingTop;
      let paddingBottom;
      let startIndex;
      let endIndex;
      let current = 0;
      while (current < len) {
        const endPos = offsetList[current + 1];
        if (endPos > start) {
          startIndex = current;
          break;
        }
        current++;
      }
      while (current < len) {
        const startPos = offsetList[current];
        if (startPos > end) {
          endIndex = current;
          break;
        }
        current++;
      }
      if (endIndex == null) endIndex = len;
      paddingTop = offsetList[startIndex];
      paddingBottom = offsetList[offsetList.length - 1] - offsetList[endIndex];
      if (paddingBottom <= loadMoreOffset) this.$emit('loadMore');

      this.paddingTop = paddingTop;
      this.paddingBottom = paddingBottom;
      this.vlist = this.list.slice(startIndex, endIndex);
    },
  },
};
/**
 * TODO:
 * 
 * 下拉刷新
 * 回到顶部
 * 横向滚动
 * 一行多于一个
 */
</script>

<style scoped>
.vlist {
  overflow: hidden;
}
.vlist--y {
  overflow-y: auto;
}
.vlist--x {
  overflow-x: auto;
}
</style>
