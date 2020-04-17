<template>
  <button
    type="button"
    data-type="italic"
    class="gte_btn gte_item"
    :class="{'gte_active': active}"
    @click="setFormat()"
  >
    <svg viewBox="0 0 320 512">
      <path
        fill="currentColor"
        d="M320 48v16a16 16 0 0 1-16 16h-67l-88 352h59a16 16 0 0 1 16 16v16a16 16 0 0 1-16 16H16a16 16 0 0 1-16-16v-16a16 16 0 0 1 16-16h67l88-352h-59a16 16 0 0 1-16-16V48a16 16 0 0 1 16-16h192a16 16 0 0 1 16 16z"
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
        if (document.queryCommandState("italic")) {
          this.active = true;
          return;
        }
      }
      this.active = false;
    },
    setFormat() {
      let $el = event.target.closest("[data-type]");
      this.$emit("statusFormatText", {
        type: "italic",
        $el: $el
      });
    }
  }
};
</script>
