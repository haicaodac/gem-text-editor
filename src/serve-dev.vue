<template>
  <div id="app">
    <GemTextEditor ref="editor" v-model="body" :init="initSettings" @uploadImages="uploadImages" />
    <button @click="getContent">Get Content</button>
  </div>
</template>

<script>
import Vue from "vue";
import GemTextEditor from "@/gem-text-editor.vue";

export default Vue.extend({
  name: "ServeDev",
  components: {
    GemTextEditor
  },
  data() {
    return {
      initSettings: {
        options: [
          "bold",
          "italic",
          "underline",
          "justifyLeft",
          "justifyCenter",
          "justifyRight",
          "orderedList",
          "unorderedList",
          "color",
          "link",
          "image"
        ],
        maxHeight: "unset",
        stickyTool: false
      },
      body: "Hello word!"
    };
  },
  watch: {
    body(newVal) {
      console.log(newVal);
    }
  },
  methods: {
    uploadImages(images) {
      setTimeout(() => {
        let url =
          "https://image.shutterstock.com/image-photo/bright-spring-view-cameo-island-260nw-1048185397.jpg";
        this.$refs.editor.insertImage({
          src: url,
          alt: "atl",
          title: "title"
        });
      }, 2000);
    },
    getContent() {
      let content = this.$refs.editor.getContent();
      console.log(content);
      // this.body = "ABC";
    }
  }
});
</script>

<style lang="scss" scoped>
#app {
  width: 700px;
  margin: 100px auto;
}
</style>
