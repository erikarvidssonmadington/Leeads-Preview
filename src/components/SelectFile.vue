<template>
  <div class="container">
    <input type="file" ref="input" @change="(e) => dropHandler(e.target)" />
    <div class="uploadBox" ref="upload">
      <div class="info">
        <div class="uploadIcon"></div>
        <div class="text">
          Dra och släpp fil för att testa material på skärmen
        </div>
        <div class="button">eller <span>Välj fil att ladda upp</span></div>
      </div>
      <div class="icon"></div>
    </div>
  </div>
</template>
<script lang="ts">
import { ref } from "@vue/reactivity";
import type Screen from "../types/Screen";
export default {
  setup() {
    const events = ref<Array<string>>([
      "dragenter",
      "dragover",
      "dragleave",
      "drop",
      "click",
    ]);
    return { events };
  },
  props: {
    fileType: Object,
    screen: Object,
  },
  mounted() {
    this.events.forEach((evName: string) => {
      (<HTMLDivElement>this.$refs.upload).addEventListener(evName, (e: any) => {
        if (evName == "drop") {
          //   e.files = e.dataTransfer.files;
          this.dropHandler(e);
        }
        if (evName == "dragenter") {
          e.target.classList.add("over");
          (<HTMLDivElement>this.$refs.upload).classList.add("over");
        }
        if (evName == "dragleave") {
          e.target.classList.remove("over");
          (<HTMLDivElement>this.$refs.upload).classList.remove("over");
        }
        if (evName == "click") {
          (<HTMLDivElement>this.$refs.input).click();
        }
        e.preventDefault();
      });
    });
  },
  methods: {
    dropHandler(e: any) {
      let file: any;
      if (e.tagName == "INPUT") {
        file = e.files[0];
      } else {
        file = e.dataTransfer.files[0];
        e.preventDefault();
      }
      let imgValidExtensions = [
        "image/jpeg",
        "image/jpeg",
        "image/jpg",
        "image/png",
        "image/webp",
      ];
      let movValidExtensions = ["video/mp4"];
      let format;
      if (this.screen && this.screen.fileType.includes('.mp4')) {
        format = () => {
          switch (true) {
            case imgValidExtensions.includes(file.type):
              return "img";
              break;
            case movValidExtensions.includes(file.type):
              return "mov";
              break;

            default:
              return;
              break;
          }
        };
      } else {
        format = () => {
          switch (true) {
            case imgValidExtensions.includes(file.type):
              return "img";
              break;
            default:
              return;
              break;
          }
        };
      }
      if (format() != "mov" && format() != "img") {
        alert("This is not a valid file format!");
        return;
      } else {
        this.$emit("setFile", { waiting: false });
      }
      this.$emit("setFile", {
        name: file.type.split("/")[0],
        type: file.type,
        file: file,
      });
    },
  },
  computed: {},
  watch: {
    fileType(newValue, oldValue) {
      //   console.log(newValue, oldValue);
    },
  },
};
</script>   
<style scoped lang="scss">
.container {
  position: absolute;
  width: 100vw;
  //   height: 100vh;
  height: calc(100vh - 140px);
  top: 0;
  left: 0;
  pointer-events: none;
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.2), rgba(0, 0, 0, 0.2));

  &.waitingForFile {}

  input {
    opacity: 0;
    pointer-events: none;
  }

  .uploadBox {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: clamp(500px, 26.042vw, 26.042vw);
    height: clamp(350px, 18.229vw, 18.229vw);
    background: rgba(13, 210, 144, 0.9);
    backdrop-filter: blur(5px);
    border-radius: 0.781vw;
    pointer-events: all;
    cursor: pointer;

    &>* {
      pointer-events: none;
    }

    .info {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: clamp(460px, 23.958vw, 23.958vw);
      height: clamp(310px, 16.146vw, 16.146vw);
      border: 0.104vw dashed #ffffff;
      border-radius: 0.781vw;
      transition: all 0.23s ease-out;

      >* {
        transition: all 0.23s ease-out;
      }

      .uploadIcon {
        position: absolute;
        top: clamp(108px, 5.625vw, 5.625vw);
        left: 50%;
        width: clamp(50px, 2.604vw, 2.604vw);
        height: clamp(50px, 2.604vw, 2.604vw);
        transform: translate(-50%, -0%);
        background: url("/assets/drop.svg");
        background-size: cover;
      }

      .text {
        position: absolute;
        font-weight: 600;
        color: #fff;
        width: clamp(320px, 16.667vw, 16.667vw);
        top: clamp(183px, 9.531vw, 9.531vw);
        left: 50%;
        transform: translate(-50%, 0%);
        font-size: clamp(24px, 1.25vw, 1.25vw);
        line-height: 122%;
        text-align: center;
      }

      .button {
        position: absolute;
        color: #fff;
        font-size: clamp(18px, 0.938vw, 0.938vw);
        font-weight: 400;
        // top: 15.052vw;
        top: clamp(264px, 13.952vw, 13.952vw);
        left: 50%;
        transform: translate(-50%, 0%);
        text-align: center;

        span {
          font-weight: 600;
          text-decoration: underline;
        }
      }
    }

    .icon {
      opacity: 0;
      transition: opacity 0.23s ease-in;
      transition: all 0.53s ease-out;
      position: absolute;
      top: 50%;
      left: 50%;
      width: 2.344vw;
      height: 2.344vw;
      transform: translate(-50%, -50%);
    }

    // &:hover {
    //   .info {
    //     opacity: 0;
    //   }

    //   .icon {
    //     opacity: 1;
    //     position: absolute;
    //     top: 50%;
    //     left: 50%;
    //     width: 3.906vw;
    //     height: 3.906vw;
    //     transform: translate(-50%, -50%);
    //     background: url("assets/leeds.svg");
    //     background-size: cover;
    //   }
    // }
    &.over {
      .info {
        width: clamp(480px, 25vw, 25vw);
        height: clamp(330px, 17.188vw, 17.188vw);

        &>* {
          opacity: 0;
        }
      }

      .text {
        top: clamp(193px, 10.5vw, 10.5vw);
      }

      .button {
        top: clamp(274px, 14.8vw, 14.8vw);
      }

      .uploadIcon {
        top: clamp(80px, 3.625vw, 3.625vw);
      }

      .icon {
        opacity: 1;
        background: url("/assets/pluss.svg");
        background-size: cover;
      }
    }
  }
}
</style>
  