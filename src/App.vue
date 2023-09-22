<script lang="ts">
import { ref } from "vue";
import Previewer from "./components/Previewer.vue";
import SelectScreen from "./components/SelectScreen.vue";
import SelectFile from "./components/SelectFile.vue";
import RemoveFile from "./components/RemoveFile.vue";
import CreateLink from "./components/CreateLink.vue";
import Header from "./components/Header.vue";
import type Screen from "./types/Screen";
import type FileType from "./types/FileType";

export default {
  setup() {
    const preview = ref<boolean>(false);
    const screen = ref<string>("Superback");
    const screens = ref<Array<Screen>>([
      {
        name: "fridhemsplan",
        img: "assets/fridhemsplan.png",
        res: "576x1512 px",
        resS: "150x400",
        fileType: ".mp4 .webp .jpg .png",
        src: "assets/fridhemsplan.webm",
        overlay: "assets/fridhemsplan-overlay.png",
        display: 'video',
        transformation: "matrix3d(1.09815, -0.226901, 0, -0.000255577, -0.190545, 1.26948, 0, -0.000161487, 0, 0, 1, 0, 369, 60, 0, 1)"
      },
      {
        name: "avenyn",
        img: "assets/avenyn.png",
        res: "1920x1080 px",
        resS: "550x200",
        fileType: ".mp4 .webp .jpg .png",
        src: "assets/avenyn.mp4",
        overlay: "assets/avenyn-overlay.png",
        display: 'video', 
        transformation: false
      },
      {
        name: "Superback",
        img: "assets/superback.jpg",
        res: "2500x2500 cm",
        resS: "250x250",
        fileType: ".webp .jpg .png",
        src: "assets/superback.jpg",
        overlay: "assets/superback-overlay.png",
        display: 'image',
        // transformation: "matrix3d(2.41103, 0.00806648, 0, -1.38506e-05, -0.214394, 1.89231, 0, -0.000181712, 0, 0, 1, 0, 906.979, 281.979, 0, 1)"
        transformation: "matrix3d(2.94201, 0.0433812, 0, 4.60522e-05, -0.110126, 2.35168, 0, -0.000115007, 0, 0, 1, 0, 890, 326.995, 0, 1)"
      },
      // {
      //   name: "Superback",
      //   img: "assets/superback_img.webp",
      //   res: "2500x2500 cm",
      //   resS: "250x250",
      //   fileType: ".webp .jpg .png",
      //   src: "assets/superback.webp",
      //   overlay: "assets/superback.webp",
      //   display: 'image',
      //   transformation: "matrix3d(2.41103, 0.00806648, 0, -1.38506e-05, -0.214394, 1.89231, 0, -0.000181712, 0, 0, 1, 0, 906.979, 281.979, 0, 1)"
      // },
      {
        name: "Mural",
        img: "assets/_hornstull-original.webp",
        res: "12.24x18.34 m",
        resS: "124.4x183.3",
        fileType: ".webp .jpg .png",
        src: "assets/hornstull.webm",
        overlay: "assets/hornstull-overlay-2.png",
        display: 'video',
        transformation: 'matrix3d(3.65115, 0.919262, 0, 0.00110891, -0.298098, 2.96438, 0, -0.000426816, 0, 0, 1, 0, 604, 101, 0, 1)'
        // transformation: 'matrix3d(7.21283, 2.56426, 0, 0.00179926, -0.310636, 7.16739, 0, -0.000514212, 0, 0, 1, 0, 680.99, -180.99, 0, 1)'
      },
      {
        name: "Streetliner",
        img: "assets/streetliner.jpg",
        res: "3070x670 cm",
        resS: "307x67",
        fileType: ".webp .jpg .png",
        src: "assets/streetliner.jpg",
        overlay: "assets/streetliner-overlay.png",
        display: 'image',
        transformation: 'matrix3d(0.607484, -0.60453, 0, -0.00157179, 0.0247156, 3.67212, 0, 0.000113617, 0, 0, 1, 0, 747, 420, 0, 1)'
        // transformation: 'matrix3d(0.26171, -0.519808, 0, -0.00159138, 0.113761, 3.18245, 0, 0.000164366, 0, 0, 1, 0, 782.969, 355.955, 0, 1)'
      },
      // {
      //   name: "Streetliner",
      //   img: "assets/streetline.webp",
      //   res: "3070x670 cm",
      //   resS: "307x67",
      //   fileType: ".webp .jpg .png",
      //   src: "assets/_Streetliner-bg.webp",
      //   overlay: "assets/_Streetliner-overlay.webp",
      //   display: 'image',
      //   transformation: 'matrix3d(0.26171, -0.519808, 0, -0.00159138, 0.113761, 3.18245, 0, 0.000164366, 0, 0, 1, 0, 782.969, 355.955, 0, 1)'
      // },
      {
        name: "Wall Panel Premium",
        img: "assets/wallpapper.webp",
        res: "500x700 cm",
        resS: "500x700",
        fileType: ".webp .jpg .png",
        src: "assets/_vagn1-bg.webp",
        overlay: "assets/_vagn1-overlay.webp",
        display: 'image',
        transformation: 'matrix3d(1.42852, 0.0432462, 0, 6.3831e-05, -0.0126908, 1.38486, 0, 5.64789e-07, 0, 0, 1, 0, 329.983, 63.9757, 0, 1)'
      },
      {
        name: "Regeringsgatan",
        img: "assets/_regeringsgatan-original.webp",
        res: "2016x840 px",
        resS: "201x84",
        fileType: ".mp4 .webp .webp .jpg .png",
        src: "assets/regeringsgatan.webm",
        overlay: "assets/_regeringsgatan-overlay.png",
        display: 'video',
        transformation: 'matrix3d(0.481149, -1.24369, 0, -0.0022943, -0.223768, 3.744, 0, -7.91969e-05, 0, 0, 1, 0, 735, 358, 0, 1)'
      },
      {
        name: "Uppsala",
        img: "assets/_2uppsala-original.webp",
        res: "1080x1920 px",
        resS: "108x192",
        fileType: ".mp4 .webp .webp .jpg .png",
        src: "assets/uppsala.webm",
        overlay: "assets/_uppsala-overlay.webp",
        display: 'video',
        transformation: 'matrix3d(1.15725, -0.227408, 0, -0.000370411, -0.154951, 1.56386, 0, -0.000116765, 0, 0, 1, 0, 1106.76, 187.41, 0, 1)'
      },
      {
        name: "Skanstull",
        img: "assets/_skanstull-original.webp",
        res: "1080x1920 px",
        resS: "150x400",
        fileType: ".mp4 .webp .webp .jpg .png",
        src: "assets/skanstull.webm",
        overlay: "assets/_skanstull-overlay.png",
        display: 'video',
        transformation: 'matrix3d(1.83979, -0.172379, 0, -0.00037309, -0.146424, 1.37197, 0, -5.55171e-05, 0, 0, 1, 0, 568, 209, 0, 1)'
      },
    ]);
    const fileType = ref<FileType>({
      name: "",
      type: "",
      file: "",
      loading: false,
      waiting: true,
      result: "",
    });

    return {
      preview,
      screen,
      screens,
      fileType,
    };
  },
  components: {
    SelectFile,
    SelectScreen,
    Previewer,
    RemoveFile,
    CreateLink,
    Header
  },
  beforeMount() {
    // window.madingtonCreativeTimeline = {}
    // window.madingtonCreativeTimeline.pause = function() {
      
    // }
    // window.madingtonCreativeTimeline.pause = function() {

    // }
    // window.madingtonCreativeTimeline.seek = function() {

    // }
    const queryString = window.location.search;
    const urlParams = new URLSearchParams(queryString);
  
    if (urlParams.get("preview") == "true") {
      this.screen = urlParams.get("screen") || "";
      this.preview = true;
    } 
  },
  mounted() {
  },
  methods: {
    setScreen(val: string, reset: boolean = false) {
      this.screen = val;
      this.fileType = {
        name: "",
        type: "",
        file: "",
        loading: false,
        waiting: true,
        result: "",
      };
    },
    setFile(val: any) {
      this.fileType = { ...this.fileType, ...val };
    },
  },
};
</script>

<template>
  <div class="body">
    <video v-if="screens.filter((s) => s.name == screen)[0].display == 'video'" class="vBackground"
      :src="screens.filter((s) => s.name == screen)[0].src" autoplay muted defaultMuted />
    <div v-else class="imgPreview" :style="{ background: `url('${screens.filter((s) => s.name == screen)[0].src}')` }">
    </div>
    <div v-if="screens.filter((s) => s.name == screen)[0].display == 'image'" class="imgs">
      <div class="image" :style="{ background: `url('${screens.filter((s) => s.name == screen)[0].src}')` }"></div>
    </div>

    <Previewer :key="screen" :class="{ preview: preview }" :screen="screen"
      :showScreen="screens.filter((s) => s.name == screen)[0]" :fileType="fileType" @setFile="setFile"></Previewer>
    <div v-if="screens.filter((s) => s.name == screen)[0].display == 'image'" class="imgs imgoverlay">
      <div class="overlay" :style="{ background: `url('${screens.filter((s) => s.name == screen)[0].overlay}')` }"></div>
    </div>
    <SelectFile v-if="fileType.waiting && !preview" @setFile="setFile" :screen="screens.filter((s) => s.name == screen)[0]" :fileType="fileType"></SelectFile>
    <SelectScreen v-if="!preview" :scr="screen" :screens="screens" @type="setScreen"></SelectScreen>
    <div class="buttons">
      <RemoveFile v-if="!fileType.waiting && !preview" @setFile="setFile"></RemoveFile>
      <CreateLink :screen="screen" :showScreen="screens.filter((s) => s.name == screen)[0]"
        v-if="!fileType.waiting && !preview" :fileType="fileType"></CreateLink>
    </div>
  </div>
  <Header></Header>
</template>

<style scoped lang="scss">
.body {
  height: 100vh;
  overflow: hidden;
}

h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.vBackground {
  position: absolute;
  width: 100vw;
  height: 100vh;
  object-fit: cover;
  z-index: -1;
  -webkit-filter: blur(100px);
}

.imgPreview {
  position: absolute;
  width: 100vw;
  height: 100vh;
  background-size: cover !important;
  background-position: center !important;
  background-repeat: no-repeat !important;
  z-index: -1;
  -webkit-filter: blur(100px);

}

.imgoverlay {
  z-index: 0;
}

.imgs {
  position: absolute;
  width: 100vw;
  height: calc(100vh - 140px);
  top: 0;

  .overlay {
    position: absolute;
    width: 100vw;
    // height: 100%;
    top: 50%;
    // left: 0;
    transform: translate(0, -50%);
    padding-bottom: 66.25vw;
    background-size: cover !important;
    background-position: center !important;
    background-repeat: no-repeat !important;
    z-index: -1;
  }

  .image {
    position: absolute;
    width: 100vw;
    // height: 100%;
    top: 50%;
    // left: 0;
    transform: translate(0, -50%);
    padding-bottom: 66.25vw;
    background-size: cover !important;
    background-position: center !important;
    background-repeat: no-repeat !important;
    z-index: -1;
  }
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {

  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}

.preview {
  top: 50% !important;
  transform: translate(0px, -50%) !important;

  @media (min-aspect-ratio: 16/9) {
    top: 0% !important;
    transform: translate(0px, 0%) !important;
  }
}

.buttons {
  position: absolute;
  display: flex;
  justify-content: center;
  gap: 30px;
  left: 50%;
  transform: translate(-50%, 0);
  bottom: clamp(160px, 8.333vw, 8.333vw);
  /* div {
    position: relative;
  } */
}
</style>
