<template>
  <div class="virtual_page">
    <div
      class="viewport"
      ref="viewport"
      :style="{ '--height': height + 'px', '--width': width + 'px' }"
      @scroll="onScroll"
    >
      <div class="scrollport" ref="scrollport"></div>
      <div class="table" ref="table">
        <div class="box" v-for="(item, i) in showList" :key="i">
          {{ item.n }}
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted, computed, onBeforeMount } from "vue";

// 可以动态调整行高，显示条数
let start = ref(0);
let end = ref(0);

const props = defineProps({
  count: {
    type: Number,
    default: 20,
  },
  width: {
    type: Number,
    default: 300,
  },
  height: {
    type: Number,
    default: 30,
  },
  list: {
    type: Array,
    default: new Array(20000).fill(null).map((v, i) => ({ n: i + 1 })),
  },
});

onBeforeMount(() => {
  end.value = props.count;
});

// ref  dom绑定
const viewport = ref();
const scrollport = ref();
const table = ref();

// 计算属性
const showList = computed(() => {
  return props.list.slice(start.value, end.value);
});

const onScroll = (e) => {
  // 滚动出去的向上偏移量
  let offsetTop = viewport.value.scrollTop;

  // 截取起始位置
  start.value = Math.round(offsetTop / props.height);
  //  截取结束位置
  end.value = start.value + props.count;

  //table列表向上的偏移量，因为要把table固定在可视区。

  table.value.style.transform = `translateY(${offsetTop}px)`;
};
onMounted(() => {
  viewport.value.style.height = props.count * props.height + "px";
  scrollport.value.style.height = props.list.length * props.height + "px";
});
</script>
<style lang="scss" scoped>
.viewport {
  width: var(--width);
  position: relative;
  overflow-y: scroll;
  background-color: aqua;

  .table {
    position: absolute;
    left: 0;
    top: 0;
  }
  .box {
    /* css继承 */
    height: var(--height);
  }
}
</style>
