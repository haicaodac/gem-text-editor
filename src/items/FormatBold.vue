<template>
  <button
    type="button"
    data-type="bold"
    class="gte_btn gte_item"
    :class="{'gte_active': active}"
    @click="setFormat()"
  >
    <svg viewBox="0 0 384 512">
      <path
        fill="currentColor"
        d="M314.52 238.78A119.76 119.76 0 0 0 232 32H48a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h16v352H48a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h208a128 128 0 0 0 128-128c0-49.49-28.38-91.92-69.48-113.22zM128 80h88a72 72 0 0 1 0 144h-88zm112 352H128V272h112a80 80 0 0 1 0 160z"
      />
    </svg>
  </button>
</template>
<script>
export default {
  data() {
    return {
      active: false
    };
  },
  mounted() {
    document.addEventListener("click", this.getStatusActive, false);
  },
  beforeDestroy() {
    document.removeEventListener("click", this.getStatusActive, false);
  },
  methods: {
    getStatusActive() {
      let $target = event.target;
      if ($target && $target.closest(".gt_text_editor_content")) {
        if (document.queryCommandState("bold")) {
          this.active = true;
          return;
        }
      }
      this.active = false;
    },
    setFormat() {
      let $el = event.target.closest("[data-type]");
      this.$emit("statusFormatText", {
        type: "bold",
        $el: $el
      });
    }
  }
};
</script>
