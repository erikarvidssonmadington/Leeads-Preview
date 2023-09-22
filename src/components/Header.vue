<template>
  <div class="header">
    <div class="logo" @click="goToUrl()"></div>
    <div class="backButton" @click="goToUrl()">till startsidan</div>
  </div>
</template>
<script lang="ts">
import { ref } from "@vue/reactivity";
import type Screen from "../types/Screen";
export default {
  mounted() { },
  methods: {
    goToUrl() {
      window.location.href = "https://www.leeads.com";
    },
  },
};
</script>   
<style scoped lang="scss">
.header {
  position: absolute;
  width: 100vw;
  height: 227px;
  left: 0px;
  // background: linear-gradient(180deg,
  //     rgba(0, 0, 0, 0.24) 0%,
  //     rgba(0, 0, 0, 0) 100%);
  // top: 0px;

  height: 7.292vw;
  min-height: 140px;
  left: 0px;
  bottom: 0px;
  pointer-events: none;

  .logo {
    position: absolute;
    // left: 19.531vw;
    right: 4.531vw;
    top: 33px;
    bottom: 0%;
    width: 110px;
    height: 28.71px;
    background: url(/assets/logo.svg);
    background-size: contain !important;
    background-repeat: no-repeat !important;
    background-position: top !important;
    cursor: pointer;



    // left: 2.531vw;
    /* top: 33px; */
    bottom: 0px;
    top: 50%;
    transform: translate(0px, -50%);
  }

  .backButton {
    font-family: "General Sans";
    font-style: normal;
    font-weight: 700;
    font-size: clamp(16px, 0.729vw, 0.729vw);
    line-height: 19px;
    position: absolute;
    // right: 19.531vw;
    left: 4.531vw;
    color: #fff;
    top: 45px;
    bottom: 0%;
    width: 150px;
    height: fit-content;
    cursor: pointer;
    display: flex;
    align-items: center;
    text-transform: uppercase;
    white-space: nowrap;
    pointer-events: all;


    top: 50%;
    transform: translate(0px, -50%);
    right: 2.531vw;

    &::before {
      content: "";
      background: url(/assets/arrow.svg);
      transform: rotate(180deg) translate(0, 0%);
      background-size: contain !important;
      background-repeat: no-repeat !important;
      background-position: top !important;
      position: absolute;
      // display: inline-block;
      z-index: 3;
      left: -1.2979em;
      // top: 50%3
      // top: 8%;
      width: clamp(13px, 0.62vw, 0.62vw);
      height: clamp(13px, 0.62vw, 0.62vw);
      transition: 0.25s ease-out;
      margin-top: 0.1vw;
    }

    &:hover::before {
      left: -1.5379em;
    }
  }
}

@media screen and (max-width: 1440px) {
  .header {
    height: 55px;
    min-height: auto;

    .logo {
      right: 2.531vw;
    }
    .backButton {
      left: 2.531vw;
    }
  }
}
</style>
  