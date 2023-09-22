<template>
  <div :class="screen" ref="container" class="container">
    <div v-show="loaded" :class="screen" ref="box" class="box">
      <div ref="preview" class="preview" :style="{
        background: src ? src + '#fff' : '#fff',
        transform: showScreen && showScreen.transformation ? showScreen.transformation : '',
        width: showScreen?.resS.split('x')[0] + 'px',
        height: showScreen?.resS.split('x')[1] + 'px',
        // width: showScreen?.display == 'image' ? showScreen?.resS.split('x')[0] + 'px' : '',
        // height: showScreen?.display == 'image' ? showScreen?.resS.split('x')[1] + 'px' : '',
        transformOrigin: '0% 0% !important',
        top: 0,
        left: 0,
        position: 'absolute',
      }">
        <!-- Drag the points to transform the box! -->
        <div class="loader" v-if="fileType?.loading">
          <div class="loaderImg"></div>
        </div>
        <div class="loading"></div>
        <video v-if="format == 'video'" class="video" ref="videoPrev1" :src="src" autoplay playsinline muted defaultMuted
          loop></video>
        <div v-if="format == 'img'"></div>
        <!-- <input ref="input" type="file" /> -->
      </div>
      <div v-if="screen == 'avenyn' || screen == 'tower'" ref="preview" class="preview secound" :style="{
        background: src + '#fff',
      }">
        <div class="loader" v-if="fileType?.loading">
          <div class="loaderImg"></div>
        </div>
        <video v-if="format == 'video'" class="video" ref="videoPrev2" :src="src" autoplay muted defaultMuted playsinline
          loop></video>
        <div v-if="format == 'img'"></div>
        <!-- <input ref="input" type="file" /> -->
      </div>
      <video v-if="showScreen && showScreen.display == 'video'" ref="video" :src="showScreen?.src" autoplay muted loop
        defaultMuted playsinline></video>
      <!-- <div v-else class="imgPreview" :style="{ background: `url('${showScreen?.src}')` }"> -->
      <!-- </div> -->
    </div>
  </div>
  <div v-if="showScreen && showScreen.display == 'video'" class="overlay" :class="screen" :style="{
    background: `url(${showScreen?.overlay})`,
  }"></div>
</template>
<script lang="ts">
import { ref } from "@vue/reactivity";
// import aws from "aws-sdk";
// import type FileType from "../types/FileType";
export default {
  setup() {
    const src = ref<string>();
    const loaded = ref<Boolean>(false);
    const loading = ref<Boolean>(false);
    const format = ref<string>("");

    const id = ref<String>("");
    const bucket = ref<string>("delivered-by-madington.com");
    const events = ref<Array<string>>([
      "dragenter",
      "dragover",
      "dragleave",
      "drop",
    ]);
    return { events, loaded, loading, format, id, bucket, src };
  },
  props: {
    screen: String,
    showScreen: Object,
    fileType: Object,
  },
  async mounted() {
    const queryString = window.location.search;
    const urlParams = new URLSearchParams(queryString);
    if (urlParams.get("preview") == "true") {
      this.format = urlParams.get("type") || "";
      if (urlParams.get("type") == "img") {
        this.src = `url('https://leeadspreview.s3.eu-west-1.amazonaws.com/uploads/${urlParams.get(
          "id"
        )}.${urlParams.get("format")}')`;
      }
      if (urlParams.get("type") == "video") {
        this.src = `https://leeadspreview.s3.eu-west-1.amazonaws.com/uploads/${urlParams.get(
          "id"
        )}.${urlParams.get("format")}`;
      }
      // return;
    }
    if (this.showScreen?.display == 'video') {
      (<HTMLDivElement>this.$refs.video).addEventListener("loadeddata", (e) => {
        this.loaded = true;
        // e.target.muted = true;
        setTimeout(() => {
          (<HTMLVideoElement>e.target).play();
        }, 1500);
      });
    } else {
      this.loaded = true;

    }
    (<HTMLDivElement>this.$refs.box).style.transform = `scale(${window.innerWidth / 2000
      })`;
    addEventListener("resize", () => {
      (<HTMLDivElement>this.$refs.box).style.transform = `scale(${window.innerWidth / 2000
        })`;
    });
  },
  methods: {},
  watch: {
    showScreen(newValue, oldValue) {
      // console.log(newValue, oldValue)
    },
    fileType(newValue, oldValue) {
      if (this.showScreen?.display == 'video') {
        (<HTMLVideoElement>this.$refs.video).play();
      }
      [...document.querySelectorAll("video")].map((v) => {
        v.muted = true;
      });
      if (newValue.file != oldValue.file && !newValue.waiting) {
        this.format = "loading";
        this.$emit("setFile", { loading: true });
        let imgValidExtensions = [
          "image/jpeg",
          "image/jpeg",
          "image/jpg",
          "image/png",
          "image/webp",
        ];
        let movValidExtensions = ["video/mp4"];

        let format = () => {
          switch (true) {
            case imgValidExtensions.includes(newValue.file.type):
              return "img";
              break;
            case movValidExtensions.includes(newValue.file.type):
              return "mov";
              break;

            default:
              return;
              break;
          }
        };

        if (format() == "mov") {
          this.format = "video";
          let fileReader = new FileReader();
          fileReader.onload = (e) => {
            this.src = `${fileReader.result}`;

            this.$emit("setFile", {
              name: newValue.file.type.split("/")[0],
              type: newValue.file.type,
              file: newValue.file,
              result: e.target?.result,
            });
          };
          fileReader.readAsDataURL(newValue.file);

          this.$nextTick(() => {
            (<HTMLVideoElement>this.$refs.videoPrev1).addEventListener(
              "play",
              (e: any) => {
                this.$emit("setFile", { loading: false });
                (<HTMLVideoElement>this.$refs.videoPrev2).currentTime = 0;
                (<HTMLVideoElement>this.$refs.videoPrev1).currentTime = 0;
              }
            );
            (<HTMLVideoElement>this.$refs.videoPrev2).addEventListener(
              "play",
              (e: any) => {
                this.$emit("setFile", { loading: false });
                (<HTMLVideoElement>this.$refs.videoPrev2).currentTime = 0;
                (<HTMLVideoElement>this.$refs.videoPrev1).currentTime = 0;
              }
            );
          });
        } else if (format() == "img") {
          let fileReader = new FileReader();
          fileReader.onload = (v: any) => {
            this.src = `url(${fileReader.result})`;
            this.$emit("setFile", {
              name: newValue.file.type.split("/")[0],
              type: newValue.file.type,
              file: newValue.file,
              result: fileReader.result,
            });
          };
          fileReader.readAsDataURL(newValue.file);

          // console.log(this.fileType);
          this.$emit("setFile", { loading: false });
        } else {
          // alert("This is not an image ore movie File!");
        }
      }
    },
  },
};
</script>
<style scoped lang="scss">
.container {
  // height: calc(100vh - 7.292vw);
  // max-height: calc(100vh - 140px);
  width: 100vw;
  padding-bottom: calc(1080 / 1920 * 100vw);
  top: 50%;
  transform: translate(0px, -50%);
  top: calc(50% - 70px);
  max-height: calc(100vh - 70px);


  &.Superback {
        padding-bottom: 66.25vw;
  }
  &.Streetliner {
        padding-bottom: 66.25vw;
  }
}

.overlay {
  width: 100vw;
  padding-bottom: calc(1080 / 1920 * 100vw);
  top: 50%;
  transform: translate(0px, -50%);
  top: calc(50% - 70px);
  max-height: calc(100vh - 70px);
}

.box {
  height: 100%;
  max-height: 100%;
  width: 2000px;
  position: absolute;
  top: 0;
  left: 0;
  transform-origin: 0px 0px;

  .preview {
    background-size: cover !important;
    background-repeat: no-repeat !important;
    background-position: center !important;
  }
}

video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: auto;
  object-fit: cover;
  z-index: -1;
}

.imgPreview {
  position: absolute;
  width: 100vw;
  // padding-bottom: 52%;
  padding-bottom: 37%;
  background-size: cover !important;
  background-position: center !important;
  background-repeat: no-repeat !important;
  z-index: -1;
  // -webkit-filter: blur(100px);
}

.overlay {
  width: 99.9%;
  // height: auto;
  position: absolute;
  // top: 0;
  // padding-bottom: calc(1080 / 1920 * 100vw);
  background-size: contain !important;
  background-repeat: no-repeat !important;
  background-position: top !important;
  pointer-events: none;
}

// .preview {
//   background-size: contain !important;
//   background-repeat: no-repeat !important;
//   background-position: top !important;
// }

.vagn {
  .overlay {
    padding-bottom: 37%;
  }
}

.fridhemsplan {
  @media screen and (min-aspect-ratio: 16/10) {
    &.overlay {
      top: 0;
      transform: translate(0, 0);
    }
  }
}

.Mural {
  &.container {
    // padding-bottom: 66.25vw;
    overflow: hidden;
  }
  &.overlay {
    // padding-bottom: 66.25vw;
    overflow: hidden;
  }
}

input {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}

input[type="file"] {
  text-indent: -999em;
  outline: none;
  width: 100%;
  height: 100%;
  position: absolute;
}

// input:hover {
//   background: rgba(168, 255, 127, 0.283);
//   width: 100%;
//   height: 100%;
// }

.video {
  width: 100%;
  height: 100%;
}

.fridhemsplan {
  @media screen and (min-aspect-ratio: 16/10) {
    &.container {
      top: 0;
      transform: translate(0, 0);
    }
  }

  .box {
    transform-origin: 0% 0% !important;
  }

  video {
    object-position: center;
  }

  .preview {
    font-size: 10px;
    color: #000;
    height: 400px;
    width: 150px;
    background-color: #fff;
    // background: url("/assets/screen.jpeg");
    background-size: cover !important;
    background-repeat: no-repeat !important;
    background-position: center !important;
    transform: matrix3d(0.798742,
        -0.191434,
        0,
        -0.000245242,
        -0.133271,
        1.00089,
        0,
        -0.000145944,
        0,
        0,
        1,
        0,
        251.979,
        125.99,
        0,
        1);
    transform-origin: 0px 0px;
  }
}

.avenyn {
  .box {
    transform-origin: 0% 0% !important;
  }

  .preview {
    position: absolute;
    top: 0;
    left: 0;
    font-size: 10px;
    color: #000;
    width: 550px;
    height: 200px;
    background-color: #fff;
    // background: url("/assets/nordby.png");
    background-size: cover !important;
    background-repeat: no-repeat !important;
    background-position: center !important;
    transform: matrix3d(1.00385,
        -0.0891776,
        0,
        -0.000106657,
        -0.000536377,
        1.1149,
        0,
        -1.47398e-5,
        0,
        0,
        1,
        0,
        376.979,
        466.979,
        0,
        1);
    transform-origin: 0px 0px;
  }

  .secound {
    // height: 200px;
    // width: 450px;
    width: 550px;
    height: 200px;
    transform: matrix3d(0.848194,
        -0.104622,
        0,
        -0.000143623,
        -0.00555493,
        1.20666,
        0,
        -2.70425e-5,
        0,
        0,
        1,
        0,
        1124.97,
        436.962,
        0,
        1);
  }
}

.tower {
  .box {
    // transform-origin: 0% 30% !important;
  }

  video {
    // position: absolute;
    // top: 0;
    // left: 0;
    // width: 100%;
    // height: 100%;
    // object-fit: cover;
    // object-position: 80% 30%;
  }

  .preview {
    position: absolute;
    top: 0;
    left: 0;
    font-size: 10px;
    color: #000;
    width: 480px;
    height: 520px;
    background-color: #fff;
    // background: url("/assets/w.jpg");
    background-size: cover !important;
    background-repeat: no-repeat !important;
    background-position: 0% !important;
    transform-origin: 0 0;
    transform: matrix3d(-0.290646,
        -0.177671,
        0,
        -0.000307947,
        0.0171442,
        0.695315,
        0,
        1.07821e-5,
        0,
        0,
        1,
        0,
        1231.98,
        276.979,
        0,
        1);
    background-position: left;

    &:nth-of-type(2) {
      background-position: 100% !important;
    }

    .video {
      object-position: left;
    }
  }

  .secound {
    width: 480px;
    height: 520px;
    background-position: right;
    transform: matrix3d(0.545225,
        -0.000953258,
        0,
        5.02256e-6,
        0.0172307,
        0.810561,
        0,
        7.42867e-6,
        0,
        0,
        1,
        0,
        1280,
        226,
        0,
        1);

    .video {
      object-position: right;
    }
  }
}

.loader {
  opacity: 1;
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgb(255, 255, 255);
  display: flex;
  justify-content: center;
  align-items: center;
}

.loaderImg {
  width: 20%;
  height: 20%;
  background: url("https://assets.website-files.com/59cb84cae4dab000012daad0/5a00a5ce519aa5000148322f_preloader.svg");
  background-repeat: no-repeat;
  background-size: contain;
}
</style>
