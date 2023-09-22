<template>
  <div v-if="createdLink == 'false'" class="button" @click="onUpload()">
    Skapa delningslänk
  </div>
  <div
    v-if="createdLink == 'loading'"
    class="button dot-flashing"
    @click="copyLink()"
  >
    Skapar delningslänk
  </div>
  <div v-if="createdLink == 'true'" class="shareButton">
    <div class="text">Delningslänk</div>
    <div
      class="button share"
      :class="{ check: copyText == copyTexts?.copied }"
      @click="copyLink()"
    >
      {{ copyText }}
    </div>
    <div class="button close" @click="createdLink = 'false'"></div>
  </div>
</template>

<script lang="ts">
import { ref } from "@vue/reactivity";
export default {
  setup() {
    interface CopyTexts {
      copy: string;
      copied: string;
    }
    const createdLink = ref<string>("false");
    const id = ref<string>("");
    const newFile = ref<boolean>(false);
    const copyTexts = ref<CopyTexts>({
      copy: "Kopiera länk",
      copied: "Länk kopierad",
    });

    const copyText = ref(copyTexts.value.copy);

    return { createdLink, id, copyText, copyTexts, newFile };
  },
  props: {
    screen: String,
    showScreen: Object,
    fileType: Object,
  },
  methods: {
    generateId() {
      this.id = String(
        Date.now().toString(32) + Math.random().toString(16)
      ).replace(/\./g, "");

      return this.id;
    },
    copyLink() {
      let format = () => {
        switch (true) {
          case this.fileType?.type.includes("image"):
            return "img";
            break;
          case this.fileType?.type.includes("video"):
            return "video";
            break;

          default:
            return;
            break;
        }
      };

      let link = `${window.location.href}?preview=true&id=${this.id}&format=${
        this.fileType?.file.name.split(".")[
          this.fileType?.file.name.split(".").length - 1
        ]
      }&type=${format()}&screen=${this.screen}`;

      navigator.clipboard.writeText(link);

      this.copyText = this.copyTexts?.copied;

      setTimeout(() => {
        this.copyText = this.copyTexts?.copy;
      }, 3000);
      // let link = `${window.location.href}?preview=true&id=${this.id}&format=${
      //   this.fileType.type.split("/")[1]
      // }&type=${format()}&screen=${this.screen}`;
      // navigator.clipboard.writeText(link);
    },
    onUpload() {
      if (this.newFile != true) {
        this.createdLink = "true";
        return;
      }
      this.uploadToS3({
        uploadUrl:
          "https://zruz6klrk5lkv7mjnj4kppm6ym0pntdh.lambda-url.eu-west-1.on.aws/", // Leeads
          // "https://3kvn4i6nih2uf4ryjkqqa2ot3a0iitfa.lambda-url.eu-west-1.on.aws/", // cityreklam
      });
    },
    b64toBlob(b64Data: any, contentType = "", sliceSize = 512) {
      const byteCharacters = atob(b64Data);
      const byteArrays = [];

      for (
        let offset = 0;
        offset < byteCharacters.length;
        offset += sliceSize
      ) {
        const slice = byteCharacters.slice(offset, offset + sliceSize);

        const byteNumbers = new Array(slice.length);
        for (let i = 0; i < slice.length; i++) {
          byteNumbers[i] = slice.charCodeAt(i);
        }

        const byteArray = new Uint8Array(byteNumbers);
        byteArrays.push(byteArray);
      }

      const blob = new Blob(byteArrays, { type: contentType });
      return blob;
    },

    async uploadToS3(options: any) {
      this.createdLink = "loading";
      this.newFile = false;
      let type = this.fileType?.file.name.split(".");
      type = type[type.length - 1];
      const extension = this.fileType?.name;
      let formData = new FormData();
      // formData.append("name", this.generateId() + "." + type);
      // formData.append("file", this.fileType?.file);
      const response = await fetch(options.uploadUrl, {
        method: "POST",
        body: JSON.stringify({ name: this.generateId() + "." + type }),
      })
        .then((response) => response.text())
        .then((data) => data);
      // return;

      if (response) {
        this.createdLink = "true";
        // console.log(response)
        const uploadUrl = await JSON.parse(response).uploadUrl;

        // const myHeaders = new Headers({ "Content-Type": this.fileType?.file.type });
        // const response2 = await fetch(uploadUrl, {
        //   method: "PUT",
        //   // headers: myHeaders,
        //   body: this.fileType?.file,
        // });
        // return
        const xhr = new XMLHttpRequest();
        xhr.open("PUT", uploadUrl);
        xhr.upload.addEventListener("progress", (e) => {
          // progress(Math.round((e.loaded / e.total) * 90) + 10)
          console.log(Math.round((e.loaded / e.total) * 90) + 10)
        });
        xhr.setRequestHeader("Content-Type", this.fileType?.file.type);
        try {
          await new Promise((resolve: any, reject) => {
            xhr.onload = (e) =>
              xhr.status - 200 < 100
                ? resolve()
                : reject(new Error("Failed to upload"));
            xhr.onerror = (e) => reject(new Error("Failed to upload"));
            xhr.send(this.fileType?.file);
          });
          // progress(100);
          const url = new URL(uploadUrl);
          console.log(url)
          return {
            // url: `${url.protocol}//${url.host}${url.pathname}`,
            // name: this.fileType?.name,
          };
        } catch (error) {
          console.log(error);
          // we'll suppress this since we have a catch all error
        }
      }
      // Catch all error
      // error("There was an error uploading your file.");
    },
  },
  watch: {
    fileType(newValue, oldValue) {
      this.newFile = true;
    },
  },
};
</script>

<style scoped lang="scss">
.button {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  white-space: nowrap;
  // padding: 10px 30px;
      line-height: 1;
  padding-top: clamp(10px, 0.501vw, 0.501vw);
  padding-bottom: clamp(10px, 0.501vw, 0.501vw);
  padding-left: clamp(30px, 1.563vw, 1.563vw);
  padding-right: clamp(30px, 1.563vw, 1.563vw);
  white-space: nowrap;
  // padding: clamp((10px 30px), (0.501vw 1.563vw), (0.501vw 1.563vw));
  // padding: clamp((10px 30px), (0.521vw 1.563vw), (0.521vw 1.563vw));
  // padding: clamp((10px 30px), (0.521vw 1.563vw), (0.521vw 1.563vw));
  // padding: clamp((10px 30px), (0.521vw 1.563vw), (0.521vw 1.563vw));
  gap: 10px;
  background: #0dd290;
  border-radius: clamp(64px, 3.333vw, 3.333vw);
  font-family: "General Sans";
  font-style: normal;
  font-size: clamp(14px, 0.729vw, 0.729vw);
  font-weight: 600;
  color: #fff;

  //   position: absolute;
  // width: 218px;
  // width: fit-content;
  // height: 44px;
  //   left: clamp(728px, 37.917vw, 37.917vw);
  //   left: clamp(37.917vw, 37.917vw, 37.917vw);
  //   left: 56.917vw;
  //   bottom: clamp(160px, 8.333vw, 8.333vw);
  cursor: pointer;

  &.share {
    width: fit-content;
    position: absolute;
    left: clamp(24px, 1.25vw, 1.25vw);
    bottom: clamp(18px, 0.938vw, 0.938vw);

    &::before {
      background: url(/assets/copy.svg);
    }

    &.check {
      &::before {
        background: url(/assets/check.svg);
      }
    }
  }

  &.close {
    position: absolute;
    right: clamp(18px, 0.938vw, 0.938vw);
    top: clamp(18px, 0.938vw, 0.938vw);
    padding: 0;
    background: none;
    transform: rotate(90deg), scale(0.5);

    &::before {
      content: "";
      background: url(/assets/pluss.svg);
      background-size: contain !important;
      background-repeat: no-repeat !important;
      background-position: top !important;
      transform: rotate(45deg);
      width: clamp(21px, 1.094vw, 1.094vw);
      height: clamp(21px, 1.094vw, 1.094vw);
      transform-origin: top right;
      left: auto;
      right: -9px;
      top: 4px;
    }
  }
}
.button::before {
  content: "";
  width: clamp(17px, 0.885vw, 0.885vw);
  height: clamp(17px, 0.885vw, 0.885vw);
  background: url(/assets/cre.svg);
  background-size: contain !important;
  background-repeat: no-repeat !important;
  background-position: top !important;
  // content: url(./assets/remove.svg);
  position: relative; /*or absolute*/
  z-index: 3;
  left: -9px;
  top: 1px;
}

.shareButton {
  width: clamp(658px, 34.271vw, 34.271vw);
  height: clamp(105px, 5.469vw, 5.469vw);
  position: absolute;
  left: 50%;
  bottom: 0;
  z-index: 9;
  transform: translate(-50%, 0%);
  background: rgba(46, 50, 60, 0.97);
  backdrop-filter: blur(5px);
  border-radius: 15px;

  .text {
    position: absolute;
    top: 12.56px;
    left: 24.37px;
    left: clamp(24px, 1.25vw, 1.25vw);
    bottom: clamp(12px, 0.625vw, 0.625vw);
    font-family: "General Sans";
    font-style: normal;
    font-weight: 600;
    font-size: clamp(18px, 0.938vw, 0.938vw);
    line-height: 24px;
    text-align: center;

    color: #fdfffe;
  }
}

.dot-flashing {
  // position: relative;
  // width: 10px;
  // height: 10px;
  // border-radius: 5px;
  // background-color: #9880ff;
  // color: #9880ff;
  // animation: dot-flashing 1s infinite linear alternate;
  padding: 0 66px 0 30px;
  animation-delay: 0.5s;
}
.dot-flashing::before,
.dot-flashing::after {
  content: "";
  display: inline-block;
  position: absolute;
  top: 0;
}
.dot-flashing::before {
  left: 87%;
  top: 50%;
  transform: translate(0, -50%);
  width: 8px;
  height: 8px;
  border-radius: 5px;
  // background-color: #9880ff;
  // color: #9880ff;
  animation: dot-flashing 1s infinite;
  animation-delay: 0s;
}
.dot-flashing::after {
  left: 81%;
  top: 50%;
  transform: translate(0, -50%);
  width: 8px;
  height: 8px;
  border-radius: 5px;
  // background-color: #9880ff;
  // color: #9880ff;
  animation: dot-flashing 1s infinite;
  animation-delay: 0.5s;
}

@keyframes dot-flashing {
  0% {
    background-color: #56f9c2;
  }
  50%,
  100% {
    background-color: rgba(255, 255, 255, 0.781);
  }
}
</style>