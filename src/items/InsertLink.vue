<template>
  <div data-type="link" class="gte_item">
    <button
      type="button"
      ref="editorBtnLink"
      class="gte_btn gte_item-btn gte_editor_insert_link"
      :class="{'gte_active': statusShowPopup}"
      @click="showPopup()"
    >
      <svg viewBox="0 0 512 512">
        <path
          fill="currentColor"
          d="M314.222 197.78c51.091 51.091 54.377 132.287 9.75 187.16-6.242 7.73-2.784 3.865-84.94 86.02-54.696 54.696-143.266 54.745-197.99 0-54.711-54.69-54.734-143.255 0-197.99 32.773-32.773 51.835-51.899 63.409-63.457 7.463-7.452 20.331-2.354 20.486 8.192a173.31 173.31 0 0 0 4.746 37.828c.966 4.029-.272 8.269-3.202 11.198L80.632 312.57c-32.755 32.775-32.887 85.892 0 118.8 32.775 32.755 85.892 32.887 118.8 0l75.19-75.2c32.718-32.725 32.777-86.013 0-118.79a83.722 83.722 0 0 0-22.814-16.229c-4.623-2.233-7.182-7.25-6.561-12.346 1.356-11.122 6.296-21.885 14.815-30.405l4.375-4.375c3.625-3.626 9.177-4.594 13.76-2.294 12.999 6.524 25.187 15.211 36.025 26.049zM470.958 41.04c-54.724-54.745-143.294-54.696-197.99 0-82.156 82.156-78.698 78.29-84.94 86.02-44.627 54.873-41.341 136.069 9.75 187.16 10.838 10.838 23.026 19.525 36.025 26.049 4.582 2.3 10.134 1.331 13.76-2.294l4.375-4.375c8.52-8.519 13.459-19.283 14.815-30.405.621-5.096-1.938-10.113-6.561-12.346a83.706 83.706 0 0 1-22.814-16.229c-32.777-32.777-32.718-86.065 0-118.79l75.19-75.2c32.908-32.887 86.025-32.755 118.8 0 32.887 32.908 32.755 86.025 0 118.8l-45.848 45.84c-2.93 2.929-4.168 7.169-3.202 11.198a173.31 173.31 0 0 1 4.746 37.828c.155 10.546 13.023 15.644 20.486 8.192 11.574-11.558 30.636-30.684 63.409-63.457 54.733-54.735 54.71-143.3-.001-197.991z"
        />
      </svg>
    </button>

    <PopupInsertLink
      v-if="statusShowPopup"
      :valueUrl="valUrl"
      :valueTitle="valTitle"
      :valueText="valText"
      :valueTarget="valTarget"
      @submitFormInsertLink="submitForm"
      @close="closePopup"
    />
  </div>
</template>
<script>
import PopupInsertLink from "./popup/PopupInsertLink.vue";

export default {
  components: {
    PopupInsertLink
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
      valUrl: "",
      valTitle: "",
      valTarget: "",
      valText: ""
    };
  },
  methods: {
    submitForm(data) {
      let $el = this.$refs.editorBtnLink;
      this.$emit("statusFormatText", {
        type: "createLink",
        $el: $el,
        url: data.url,
        text: data.text,
        title: data.title,
        target: data.target
      });
      this.statusShowPopup = false;
    },
    closePopup() {
      this.statusShowPopup = false;
    },
    showPopup() {
      let selection = window.getSelection();
      if (selection.type == "Range") {
        var container = document.createElement("div");
        container.appendChild(selection.getRangeAt(0).cloneContents());
        this.valText = container.innerHTML;
      }
      if (selection.type == "Caret") {
        this.valText = "";
      }

      this.statusShowPopup = !this.statusShowPopup;
    }
  }
};
</script>
<style lang="scss" scoped>
.gte_editor_insert_link {
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  width: 100%;
  height: 100%;
  border-radius: 5px;
}
</style>
