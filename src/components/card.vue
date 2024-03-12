<template>
  <div
    class="card"
    :style="isDock?{}:{position:'absolute',zIndex:node.zIndex,top:`${node.top}px`,left:`${node.left}px`}"
    @click="handClick"
  >
    <!-- 
      style="width: 40px;height: 40px;" -->
    <img
      :src="IMG_MAP[node.type]"
      width="40"
      height="40"
      :alt="`${node.type}`"
    >
    <div
      v-if="isFreeze"
      class="mask"
    ></div>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, defineEmits, computed, defineProps } from "vue";
onMounted(() => {});
interface Props {
  node: CardNode;
  isDock?: boolean;
}
// 下面是定义调用父组件的下穿的方法的
const emit = defineEmits(["clickCard"]);
const props = defineProps<Props>();
// 加载图片资源
const modules = import.meta.glob("../assets/tutu2/*.png", {
  as: "url",
  import: "default",
  eager: true,
});
const IMG_MAP = Object.keys(modules).reduce((acc, cur) => {
  const key = cur.replace("../assets/tutu2/", "").replace(".png", "");
  acc[key] = modules[cur];
  return acc;
}, {} as Record<string, string>);
const isFreeze = computed(() => {
  // return props.node.parents.length > 0
  //   ? props.node.parents.some((it) => {
  //       it.state < 2;
  //     })
  //   : false;
  if (props.node.parents.length != 0) {
    let isClicked = props.node.parents.findIndex((it) => it.state != 2);
    if (isClicked != -1) {
      return true;
    } else {
      return false;
    }
  }
});
function handClick() {
  if (!isFreeze.value) emit("clickCard", props.node);
}
</script>
<style lang="scss" scoped>
.card {
  box-sizing: border-box;
  width: 40px;
  height: 40px;
  display: flex;
  background: #d2f7d8;
  color: #d2f7d8;
  align-items: center;
  justify-content: center;
  position: relative;
  border-radius: 4px;
  border: 1px solid #a9ecb4;
  box-shadow: 1px 5px 5px -1px #8f8f8f;
  cursor: pointer;
}
img {
  border-radius: 4px;
}
.mask {
  position: absolute;
  z-index: 1;
  top: 0;
  left: 0;
  width: 40px;
  height: 40px;
  pointer-events: none;
  background-color: rgba(0, 0, 0, 0.55);
}
</style>