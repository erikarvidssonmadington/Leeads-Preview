<template>
  <div class="button" @click="emit()">Ladda upp ny fil</div>
</template>

<script lang="ts">
import { ref } from "@vue/reactivity";
export default {
  setup() {},
  props: {
    screen: String,
    showScreen: Object,
    fileType: Object,
  },
  methods: {
    emit() {
      this.$emit("setFile", { name: "", type: "", file: "", waiting: true });
    },
  },
};
</script>

<style scoped lang="scss">
.button {
  display: flex;
  flex-direction: row;
  justify-content: center;
  line-height: 1;
  align-items: center;
  padding: 10px 30px;
  gap: 10px;
  background: #2e323c;
  background: transparent;
  border: 2px solid #fff;
  border-radius: clamp(64px, 3.333vw, 3.333vw);
  font-family: "General Sans";
  font-style: normal;
  font-weight: 600;
  color: #fff;
  padding-top: clamp(10px, 0.501vw, 0.501vw);
  padding-bottom: clamp(10px, 0.501vw, 0.501vw);
  padding-left: clamp(30px, 1.563vw, 1.563vw);
  padding-right: clamp(30px, 1.563vw, 1.563vw);
  white-space: nowrap;

  // position: absolute;
  // width: 218px;
  // height: 44px;
  //   left: clamp(728px, 37.917vw, 37.917vw);
  // left: clamp(37.917vw, 37.917vw, 37.917vw);
  // bottom: clamp(160px, 8.333vw, 8.333vw);
  cursor: pointer;
}
.button::before {
  content: url(/assets/remove.svg);
  // content: url(./assets/remove.svg);
  position: relative; /*or absolute*/
  z-index: 3;
  left: -9px;
  top: 1px;
}
</style>