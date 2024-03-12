<template>
  <!-- <div class="game"></div> -->
  <div class="gameBox">
    <div class="gameBox-title">
      果了个果
    </div>
    <div
      ref="containerRef"
      class="gameBox-container"
    >
      <div class="gameBox-container-box">
        <template
          v-for="item in nodes"
          :key="item.id"
        >
          <transition name="slide-face">
            <Card
              v-if="[0,1].includes(item.state)"
              :node="item"
              @click-card="handleSelect"
            />
          </transition>
        </template>
      </div>
    </div>
    <transition name="bounce">
      <div
        v-if="isWin"
        class="gameBox-container-box-win"
      >
        成功加入兔圈~
      </div>
    </transition>
    <transition name="bounce">
      <div
        v-if="showTip"
        class="gameBox-container-box-win"
      >
        第{{curLevel+1}}关
      </div>
    </transition>
    <div class="gameBox-card">
      <Card
        v-for="item in removeList"
        :key="item.id"
        :node="item"
        is-dock
        @click-card="handleSelectRemove"
      />
    </div>
    <div
      class="gameBox-bounce"
      w-full
      flex
      items-center
      justufy-center
    >
      <div class="gameBox-bounce-border">
        <template
          v-for="item in selectedNodes"
          :key="item.id"
        >
          <transition name="bounce">
            <Card
              v-if="item.state===2"
              :node="item"
              is-dock
            />
          </transition>
        </template>
      </div>
    </div>
    <div class="removeBox">
      <!-- :disabled="removeFlag" -->
      <button
        class="removeBox-btn"
        @click="handleRemove"
      >移除前三个</button>
      <button
        :disabled="backFlag"
        class="removeBox-btn"
        @click="handleBack"
      >回退</button>
    </div>
    <div class="teacherBox">
      <span class="teacherBox-designer">Learn from XCC</span>
      by : wms
    </div>
    <audio
      ref="clickAudioRef"
      style="display: none;"
      src="../../public/audio/click.mp3"
    ></audio>
    <audio
      ref="dropAudioRef"
      style="display: none;"
      src="../../public/audio/drop.mp3"
    ></audio>
    <audio
      ref="winAudioRef"
      style="display: none;"
      src="../../public/audio/win.mp3"
    ></audio>
    <audio
      ref="loseAudioRef"
      style="display: none;"
      src="../../public/audio/lose.mp3"
    ></audio>
    <audio
      ref="welAudioRef"
      style="display: none;"
      src="../../public/audio/welcome.mp3"
    ></audio>
  </div>
</template>

<script lang="ts" setup>
import "./scss/home.scss";
import { ref, onMounted } from "vue";
import Card from "../components/card.vue";
import { useGame } from "../core/useGame";
import { basicCannon, schoolPride } from "../core/utils";
onMounted(() => {
  initData();
});
const containerRef = ref<HTMLElement | undefined>();
const clickAudioRef = ref<HTMLAudioElement | undefined>();
const dropAudioRef = ref<HTMLAudioElement | undefined>();
const winAudioRef = ref<HTMLAudioElement | undefined>();
const loseAudioRef = ref<HTMLAudioElement | undefined>();
const welAudioRef = ref<HTMLAudioElement | undefined>();
const curLevel = ref(1);
const showTip = ref(false);
const isWin = ref(false);
const LevelConfig = [
  { cardNum: 4, layerNum: 2, trap: false },
  { cardNum: 9, layerNum: 3, trap: false },
  { cardNum: 15, layerNum: 6, trap: false },
  { cardNum: 25, layerNum: 10, trap: false },
];
const {
  nodes,
  selectedNodes,
  handleSelect,
  handleBack,
  backFlag,
  handleRemove,
  removeFlag,
  removeList,
  handleSelectRemove,
  initData,
} = useGame({
  container: containerRef,
  cardNum: 4,
  layerNum: 2,
  trap: false,
  events: {
    clickCallback: handleClickCard,
    dropCallback: handleDropCard,
    winCallback: handleWin,
    loseCallback: handleLose,
  },
});
function handleClickCard() {
  if (clickAudioRef.value?.pause) {
    clickAudioRef.value.play();
  } else if (clickAudioRef.value) {
    clickAudioRef.value.load();
    clickAudioRef.value.play();
  }
}
function handleDropCard() {
  dropAudioRef.value?.play();
}
function handleWin() {
  winAudioRef.value?.play();
  if (curLevel.value < LevelConfig.length) {
    basicCannon();
    showTip.value = true;
    setTimeout(() => {
      showTip.value = false;
    }, 1500);
    setTimeout(() => {
      initData(LevelConfig[curLevel.value]);
      curLevel.value++;
    }, 2000);
  } else {
    isWin.value = true;
    schoolPride();
  }
}
function handleLose() {
  loseAudioRef.value?.play();
  setTimeout(() => {
    alert("槽位已满，再接再厉~");
    nodes.value = [];
    removeList.value = [];
    selectedNodes.value = [];
    welAudioRef.value?.play();
    curLevel.value = 0;
    showTip.value = true;
    setTimeout(() => {
      showTip.value = false;
    }, 1500);
    setTimeout(() => {
      initData(LevelConfig[curLevel.value]);
      curLevel.value++;
    }, 2000);
  }, 500);
}
// 下面是定义调用父组件的下穿的方法的
// const emit = defineEmits<{
//     (e: 'on-click', name: string): void
// }>()
// 下面是暴露数据或者方法给父组件使用
//defineExpose({
//    list
//})
</script>
<style lang="scss" scoped>
.game {
  box-sizing: border-box;
  width: 100vh;
  height: 100vh;
  background-color: rgb(187, 241, 216);
  position: relative;
  left: 50%;
  top: 4px;
  transform: translateX(-50%);
  border: 2px solid rgb(153, 178, 182);
}
</style>