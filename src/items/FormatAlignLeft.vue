<template>
  <button
    role="button"
    data-type="justifyLeft"
    class="gte_btn gte_item"
    :class="{'gte_active': active}"
    @click="setFormat()"
  >
    <svg viewBox="0 0 448 512">
      <path
        fill="currentColor"
        d="M12.83 344h262.34A12.82 12.82 0 0 0 288 331.17v-22.34A12.82 12.82 0 0 0 275.17 296H12.83A12.82 12.82 0 0 0 0 308.83v22.34A12.82 12.82 0 0 0 12.83 344zm0-256h262.34A12.82 12.82 0 0 0 288 75.17V52.83A12.82 12.82 0 0 0 275.17 40H12.83A12.82 12.82 0 0 0 0 52.83v22.34A12.82 12.82 0 0 0 12.83 88zM432 168H16a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h416a16 16 0 0 0 16-16v-16a16 16 0 0 0-16-16zm0 256H16a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h416a16 16 0 0 0 16-16v-16a16 16 0 0 0-16-16z"
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
      if ($target && $target.closest(".gte_editor_content")) {
        if (document.queryCommandState("justifyLeft")) {
          this.active = true;
          return;
        }
      }
      this.active = false;
    },
    setFormat() {
      var $el = event.target.closest("[data-type]");
      this.$emit("statusFormatText", {
        type: "justifyLeft",
        $el: $el
      });
    }
  }
};
</script>
