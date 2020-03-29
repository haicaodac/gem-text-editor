<template>
  <button
    type="button"
    data-type="insertUnorderedList"
    class="gte_btn gte_item"
    :class="{'gte_active': active}"
    @click="setFormat()"
  >
    <svg viewBox="0 0 512 512">
      <path
        fill="currentColor"
        d="M48 368a48 48 0 1 0 48 48 48 48 0 0 0-48-48zm0-160a48 48 0 1 0 48 48 48 48 0 0 0-48-48zm0-160a48 48 0 1 0 48 48 48 48 0 0 0-48-48zm448 24H176a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h320a16 16 0 0 0 16-16V88a16 16 0 0 0-16-16zm0 160H176a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h320a16 16 0 0 0 16-16v-16a16 16 0 0 0-16-16zm0 160H176a16 16 0 0 0-16 16v16a16 16 0 0 0 16 16h320a16 16 0 0 0 16-16v-16a16 16 0 0 0-16-16z"
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
        if (document.queryCommandState("insertUnorderedList")) {
          this.active = true;
          return;
        }
      }
      this.active = false;
    },
    setFormat() {
      var $el = event.target.closest("[data-type]");
      this.$emit("statusFormatText", {
        type: "insertUnorderedList",
        $el: $el
      });
    }
  }
};
</script>
