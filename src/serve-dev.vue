<script>
import Vue from "vue";
import TextEditor from "@/text-editor.vue";

export default Vue.extend({
  name: "ServeDev",
  components: {
    TextEditor
  }
});
</script>

<template>
  <div id="app">
    <text-editor />
  </div>
</template>
