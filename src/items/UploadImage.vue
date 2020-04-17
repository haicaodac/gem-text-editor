<template>
  <div data-type="image" class="gte_item">
    <button
      type="button"
      class="gte_btn gte_item-btn gte_editor_insert_image"
      :class="{'gte_active': statusShowPopup}"
      @click="showPopup()"
    >
      <svg viewBox="0 0 512 512">
        <path
          fill="currentColor"
          d="M464 64H48C21.49 64 0 85.49 0 112v288c0 26.51 21.49 48 48 48h416c26.51 0 48-21.49 48-48V112c0-26.51-21.49-48-48-48zm-6 336H54a6 6 0 0 1-6-6V118a6 6 0 0 1 6-6h404a6 6 0 0 1 6 6v276a6 6 0 0 1-6 6zM128 152c-22.091 0-40 17.909-40 40s17.909 40 40 40 40-17.909 40-40-17.909-40-40-40zM96 352h320v-80l-87.515-87.515c-4.686-4.686-12.284-4.686-16.971 0L192 304l-39.515-39.515c-4.686-4.686-12.284-4.686-16.971 0L96 304v48z"
        />
      </svg>
    </button>

    <PopupUploadImage
      v-if="statusShowPopup"
      :valueUrl="valUrl"
      @uploadImage="submitForm"
      @close="closePopup"
    />
  </div>
</template>
<script>
import PopupUploadImage from "./popup/PopupUploadImage.vue";

export default {
  components: {
    PopupUploadImage
  },
  props: {
    status: {
      type: Boolean,
      required: false,
      default() {
        return false;
      }
    }
  },
  data() {
    return {
      active: false,
      statusShowPopup: false,
      valUrl: ""
    };
  },
  methods: {
    submitForm(data) {
      let $el = document.querySelector(".gte_editor_insert_image");
      this.$emit("statusFormatText", {
        type: "uploadImage",
        $el: $el,
        files: data.files
      });
      this.statusShowPopup = false;
    },
    closePopup() {
      this.statusShowPopup = false;
    },
    showPopup() {
      this.statusShowPopup = !this.statusShowPopup;
    }
  }
};
</script>
<style lang="scss" scoped>
.gte_editor_insert_image {
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  width: 100%;
  height: 100%;
  border-radius: 5px;
}
</style>
