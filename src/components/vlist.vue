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
    getSize: {
      type: Function,
      required: true,
    },
    direction: {
      default: 'y',
    },
  },
  data() {
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
    this.setOffsetList();
  },
  mounted() {
    const { width, height } = this.$el.getClientRects()[0];
    this.w = width;
    this.h = height;
    this.scrollPos = 0;
    this.doSetVList();
    this.setVList = throttle(this.doSetVList, 300);
  },
  computed: {
    listCls() {
      return [this.direction === 'y' ? 'vlist--y' : 'vlist--x'];
    },
  },
  methods: {
    setOffsetList() {
      const { list, getSize } = this;
      const offsetList = [];
      let lastOffset = 0;
      offsetList.push(lastOffset);
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
      this.setVList();
    },
    doSetVList() {
      const start = this.scrollPos;
      const end = this.scrollPos + this.h;
      const { offsetList } = this;
      let paddingTop;
      let paddingBottom;
      let startIndex;
      let endIndex;
      const len = this.list.length;
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
      // TODO: 前后多渲染几个
      paddingTop = offsetList[startIndex];
      paddingBottom = offsetList[offsetList.length - 1] - offsetList[endIndex];

      this.paddingTop = paddingTop;
      this.paddingBottom = paddingBottom;
      this.vlist = this.list.slice(startIndex, endIndex);
    },
  },
};
/**
 * TODO:
 * 
 * resize事件
 * 横向滚动
 * 一行多于一个
 * watch list change
 * 下来刷新
 * 上拉加载
 * 回到顶部
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
