<template>
  <div class="buttonContainer">
    <div class="button next" @click="changeScreen('prev')"></div>
    <div v-for="s in screens" :class="{ selected: s.name == scr }" class="select" :style="{
      background: `url(${s.img})`,
    }" @click="$emit('type', s.name)">
      <div>
        {{ s.name }}
      </div>
    </div>
    <div class="button prev" @click="changeScreen('next')"></div>
    <div class="info">
      <div class="res">
        Upplösning <span>{{ (<any>info)[0].res }}</span>
      </div>
      <div class="format">
        Filformat <span> {{ (<any>info)[0].fileType }}</span>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
//@ts-ignore
import type Screen from "../types/Screen";
export default {
  props: {
    screens: Array<Screen>,
    scr: String
  },
  mounted() {
  },
  methods: {
    changeScreen(move: string) {
      let index = this.screens?.findIndex((s, i) => s.name == this.scr) ? this.screens?.findIndex((s, i) => s.name == this.scr) : 0
      if (move == "next") {
        //@ts-ignore
        if (index + 1 < this.screens.length) {
          //@ts-ignore
          this.$emit('type', this.screens[index + 1].name)
        } else {
          //@ts-ignore
          this.$emit('type', this.screens[0].name)
        }
      }
      if (move == "prev") {
        if (index == 0) {
          //@ts-ignore
          this.$emit('type', this.screens[this.screens.length - 1].name)
        } else {
          //@ts-ignore
          this.$emit('type', this.screens[index - 1].name)
        }
      }
    }
  },
  computed: {
    info() {
      return this.screens?.filter(e => e.name == this.scr)
    }
  },
};
</script>   
<style scoped lang="scss">
.buttonContainer {
  // margin: 20px 30px;
  background: rgba(46, 50, 60, 0.97);
  width: fit-content;
  // border-radius: 10px;
  height: 7.292vw;
  min-height: 140px;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  bottom: 0;
}

.button {
  width: 20px;
  height: 39px;
  cursor: pointer;
  //   background: #fff;
  //   position: absolute;

  &.prev {
    margin: 0 0 0 1.302vw;
    background: url("/assets/arrow.svg");
    background-repeat: no-repeat;
    background-position: center;
    // left:clamp(10%, 30.521vw, 10%);
  }

  &.next {
    margin: 0 1.302vw 0 0;
    background: url("/assets/arrow.svg");
    background-repeat: no-repeat;
    background-position: center;
    transform: rotate(180deg);
    // right: clamp(10%, 30.521vw, 10%);
  }
}

.select {
  padding: 20px 30px;
  font-size: 18px;
  text-transform: capitalize;
  width: 11.094vw;
  height: 6.198vw;
  min-width: 213.25px;
  min-height: 120.42px;
  margin: 0 0.521vw 0 0;
  background-size: contain !important;
  background-repeat: no-repeat !important;
  background-size: auto 4.844vw !important;
  // background-size: auto clamp(95px, 4.844vw, 4.844vw) !important;
  background-size: cover !important;
  background-position: bottom left !important;
  border-radius: 0.26vw;
  overflow: hidden;
  color: rgba(46, 50, 60, 0.97);
  cursor: pointer;

  &.selected {
    //   color: #fff;
    background: rgba(197, 197, 198, 0.421);

    div {
      background: #0dd290;
    }
  }

  &:nth-of-type(4) {
    // border: 2px solid orange;
  }

  &:last-of-type {
    margin: 0 0 0 0;
    border-bottom: none;
  }

  div {
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: clamp(16px, 0.833vw, 0.833vw);
    font-weight: 600;
    bottom: 0;
    left: 0;
    height: 1.563vw;
    min-height: 30.03px;
    width: 100%;
    background: #fff;
    text-align: center;
  }
}

.info {
  position: absolute;
  right: 10.781vw;
  // top: -77px;
  margin-bottom: 160px;
  height: 100%;

  //   left: 8.229vw;
  div {
    color: #fff;
    font-weight: 700;
    font-size: clamp(20px, 1.042vw, 1.042vw);
    text-align: right;

    span {
      text-align: left;
      font-weight: 400;
      margin-left: clamp(18px, 1.042vw, 1.042vw);
      width: 10vw;
      display: inline-block;
      word-spacing: 0.3vw;
      text-wrap: nowrap;
    }
  }

  @media screen and (max-width: 1870px) {
    right: 2.781vw;
  }

  @media screen and (max-width: 1370px) {
    top: -77px;
    right: 2.781vw;

    div {
      span {
        width: 140px;
      }
    }
  }

  @media screen and (max-width: 1120px) {
    // top: -208px;
    top: -143px;
    right: 0.865vw;

    div {
      span {
        width: 140px;
      }
    }
  }
}
</style>
  