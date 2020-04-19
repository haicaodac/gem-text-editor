<template>
  <div class="gte_popup_image">
    <form method="POST" action @submit.stop.prevent="submit">
      <div class="gte_popup_image_form-group">
        <div class="gte_popup_image-theme-dragdropbox">
          <input
            name="files[]"
            multiple="multiple"
            type="file"
            @change="selectFiles"
            accept="image/*"
          />
          <div class="gte_popup_image-input-dragDrop">
            <div class="gte_popup_image-input-inner">
              <div class="gte_popup_image-input-text">
                <h3>Drag & Drop files here</h3>
                <span>or</span>
              </div>
              <a class="gte_popup_image-input-choose-btn blue">Browse Files</a>
            </div>
          </div>
          <div
            v-if="files && files.length"
            class="gte_popup_image-preview-image"
            :class="{'gte_popup_image-preview-image-multi': files.length >= 2}"
          >
            <img v-for="file in files" :key="file.src" :src="file.src" :title="file.name" />
          </div>
        </div>
        <span
          class="gte_popup_image-clear"
          v-show="files && files.length"
          @click="clearFiles"
        >Remove image selected</span>
      </div>
      <div class="gte_popup_image_list_button">
        <button
          type="button"
          class="gte_btn gte_popup_image_button_cancle_insert_link"
          @click="cancleInsertLink"
        >Cancel</button>
        <button
          type="submit"
          class="gte_btn gte_btn-primary gte_popup_image_button_insert_link"
        >Upload</button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  props: {
    valueUrl: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      valUrl: "",
      valFiles: [],
      files: []
    };
  },
  mounted() {
    this.valUrl = this.valueUrl;

    document.addEventListener("click", this.checkClosePopup, false);
  },
  beforeDestroy() {
    document.removeEventListener("click", this.checkClosePopup, false);
  },
  methods: {
    checkClosePopup() {
      let $target = event.target;
      if (
        $target &&
        ($target.closest(".gte_popup_image") ||
          $target.closest('[data-type="image"]'))
      ) {
        return;
      }
      this.$emit("close");
    },
    selectFiles() {
      this.files = [];
      this.valFiles = [];
      let files = event.target.files;
      if (files && files.length) {
        for (let i = 0; i < files.length; i++) {
          let file = files[i];
          this.valFiles.push(file);
          var reader = new FileReader();
          reader.onload = e => {
            this.files.push({
              src: e.target.result,
              name: file.name
            });
          };
          reader.readAsDataURL(file);
        }
      }
      event.target.value = "";
    },
    clearFiles() {
      this.files = [];
      this.valFiles = [];
    },
    cancleInsertLink() {
      event.preventDefault();

      this.$emit("close");
    },
    submit(event) {
      var data = {
        files: this.valFiles
      };
      this.$emit("uploadImage", data);
    }
  }
};
</script>
<style lang="scss" scoped>
.gte_popup_image {
  background: #ffffff;
  border-radius: 10px;
  position: absolute;
  top: calc(100% + 9px);
  width: 240px;
  box-shadow: 0px 7px 64px rgba(0, 0, 0, 0.15);
  padding: 16px 24px;
  cursor: default;
  &::after {
    content: "";
    position: absolute;
    top: -16px;
    left: 50%;
    -webkit-transform: translateX(-50%);
    transform: translateX(-50%);
    border: solid 8px #ffffff;
    border-right: 8px solid transparent;
    border-left: 8px solid transparent;
    border-top: 8px solid transparent;
  }
  .gte_popup_image_form-group {
    margin-bottom: 14px;
    label {
      font-size: 13px;
      color: #43484b;
      margin-bottom: 5px;
    }
    .gte_popup_image-theme-dragdropbox {
      font-family: sans-serif;
      font-size: 14px;
      color: #494949;
      position: relative;
      height: 140px;
      border: 2px dashed #d6dae0;

      * {
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;
      }
      input {
        z-index: 999;
        opacity: 0;
        width: 100%;
        height: 100%;
        position: absolute;
        right: 0px;
        left: 0px;
        margin-right: auto;
        margin-left: auto;
        cursor: pointer;
      }
      .gte_popup_image-input-dragDrop {
        position: absolute;
        z-index: 99;
        width: 100%;
        height: 100%;
        box-sizing: border-box;
        color: #43484b;
        background: #fff;
        transition: box-shadow 0.3s, border-color 0.3s;
        display: flex;
        justify-content: center;
        align-items: center;
        text-align: center;
      }

      .gte_popup_image-input-text h3 {
        margin: 0;
        font-size: 18px;
      }
      .gte_popup_image-input-text span {
        font-size: 12px;
      }
      .gte_popup_image-input-choose-btn.blue {
        color: #3e50af;
        border: 1px solid #3e50af;
      }
      .gte_popup_image-input-choose-btn {
        display: inline-block;
        padding: 8px 14px;
        outline: none;
        cursor: pointer;
        text-decoration: none;
        text-align: center;
        white-space: nowrap;
        font-size: 12px;
        font-weight: bold;
        color: rgba(#43484b, 0.7);
        border-radius: 3px;
        border: 1px solid #d6dae0;
        vertical-align: middle;
        background-color: #fff;
        box-shadow: 0px 1px 5px rgba(0, 0, 0, 0.05);
        -webkit-transition: all 0.2s;
        -moz-transition: all 0.2s;
        transition: all 0.2s;
      }

      .gte_popup_image-preview-image {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 9999;
        background: #fff;
        display: flex;
        justify-content: center;
        align-items: center;
        img {
          max-width: 100%;
          max-height: 100%;
        }
        &.gte_popup_image-preview-image-multi {
          flex-wrap: wrap;
          justify-content: flex-start;
          align-items: flex-start;
          overflow-y: auto;
          img {
            height: 50%;
            margin: 2px;
          }
        }
      }
    }
  }
  .gte_popup_image-clear {
    display: block;
    font-size: 13px;
    margin-top: 10px;
    cursor: pointer;
    transition: all 0.25s;
    &:hover {
      color: #3e50af;
    }
  }
}
.gte_popup_image_list_button {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;

  .gte_popup_image_button_insert_link {
    margin-left: 8px;
  }
}
</style>
