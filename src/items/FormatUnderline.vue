<template>
  <button
    type="button"
    data-type="underline"
    class="gte_btn gte_item"
    :class="{'gte_active': active}"
    @click="setFormat()"
  >
    <svg viewBox="0 0 448 512">
      <path
        fill="currentColor"
        d="M32 48h32v208c0 88.22 71.78 160 160 160s160-71.78 160-160V48h32a16 16 0 0 0 16-16V16a16 16 0 0 0-16-16H288a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h32v208a96 96 0 0 1-192 0V48h32a16 16 0 0 0 16-16V16a16 16 0 0 0-16-16H32a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16zm400 416H16a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h416a16 16 0 0 0 16-16v-16a16 16 0 0 0-16-16z"
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
        if (document.queryCommandState("underline")) {
          this.active = true;
          return;
        }
      }
      this.active = false;
    },
    setFormat() {
      let $el = event.target.closest("[data-type");
      this.$emit("statusFormatText", {
        type: "underline",
        $el: $el
      });
    }
  }
};
</script>
