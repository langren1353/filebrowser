<template>
  <div
    ref="VideoPlayRef"
    id="dplayer"
    style="height: calc(100% - 2px); width: 100%"
  ></div>
</template>

<script>
import DPlayer from "dplayer";

export default {
  props: {
    src: {
      type: String,
      default: "",
    },
  },
  computed: {
    localKey() {
      return this.src.replace(/(t|k)=\d+/, ""); // 避免时间戳的干扰
    },
  },
  mounted() {
    this.instance = new DPlayer({
      container: this.$refs.VideoPlayRef,
      video: {
        url: this.src,
      },
    });
    // 绑定事件
    this.instance.on("play", () => {
      this.instance.seek(this.getProcess());
    });
    this.instance.on("timeupdate", () => {
      this.timeHandler(this.instance.video.currentTime);
    });
  },
  beforeDestroy() {
    this.instance.destroy();
  },
  data() {
    return {
      instance: null,
    };
  },
  methods: {
    getProcess() {
      const val = localStorage.getItem(this.localKey) || 0;
      return Math.max(val - 1, 0);
    },
    timeHandler(time) {
      if (time !== 0) {
        localStorage.setItem(this.localKey, time);
      }
    },
  },
};
</script>

<style lang="scss" scoped></style>
