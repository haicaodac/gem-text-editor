
<template>
  <div class="gte_editor">
    <div
      v-if="optionsTypes && optionsTypes.length"
      class="gte_editor-item"
      :class="{'gte_editor-item--sticky': initSettings.stickyTool}"
    >
      <component
        :is="renderControl(option.type)"
        v-for="option in optionsTypes"
        :key="option.type"
        v-bind="option"
        @statusFormatText="setFormatText"
      />
    </div>
    <div
      ref="editorContent"
      class="gte_editor_content"
      contenteditable="true"
      @blur="savePosition"
      @keyup="onKeyup"
      @mouseup="mouseup"
      :style="{'max-height': initSettings.maxHeight, 'min-height': initSettings.minHeight}"
    />
  </div>
</template>

<script>
import FormatBold from "./items/FormatBold.vue";
import FormatItalic from "./items/FormatItalic.vue";
import FormatUnderline from "./items/FormatUnderline.vue";
import FormatAlignCenter from "./items/FormatAlignCenter.vue";
import FormatAlignLeft from "./items/FormatAlignLeft.vue";
import FormatAlignRight from "./items/FormatAlignRight.vue";
import FormatColorText from "./items/FormatColorText.vue";
import FormatOrderedList from "./items/FormatOrderedList.vue";
import FormatUnorderedList from "./items/FormatUnorderedList.vue";
import InsertLink from "./items/InsertLink.vue";
import UploadImage from "./items/UploadImage.vue";

export default {
  name: "GemTextEditor", // vue component name
  components: {
    FormatBold,
    FormatItalic,
    FormatUnderline,
    FormatAlignCenter,
    FormatAlignLeft,
    FormatAlignRight,
    FormatColorText,
    FormatOrderedList,
    FormatUnorderedList,
    InsertLink
  },
  props: {
    init: {
      type: Object,
      default() {
        return {};
      }
    },
    value: {
      type: String,
      default: ""
    }
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
          "link"
          // "image"
        ],
        minHeight: {
          type: String, // 200px
          default: "200px"
        },
        maxHeight: {
          type: String, // 200px
          default: "200px"
        },
        stickyTool: false
      },

      val: "",
      sel: "",
      savedSel: "",
      ranges: [],

      optionsTypes: [],
      currentFormat: []
    };
  },
  watch: {
    value(newVal, oldVal) {
      if (newVal !== oldVal) {
        let content = this.getContent();
        if (newVal !== content) {
          this.$nextTick(() => {
            this.formatInitContent(newVal);
          });
        }
      }
    }
  },
  mounted() {
    // Merge init data
    this.initSettings = Object.assign({}, this.initSettings, this.init);
    this.formatInitContent(this.value);

    let optionsValue = [];
    if (this.initSettings.options && this.initSettings.options.length) {
      for (let index = 0; index < this.initSettings.options.length; index++) {
        let item = this.initSettings.options[index];
        optionsValue[index] = {
          type: item
        };
        if (item == "color") {
          optionsValue[index]["color"] = "#000";
        }
      }
      this.optionsTypes = optionsValue;
    }
    document.execCommand("defaultParagraphSeparator", false, "div");
  },
  methods: {
    /**
     * Format the input data to the correct editor
     */
    formatInitContent(newVal) {
      this.val = newVal;
      let $content = this.$refs.editorContent;
      if ($content) {
        $content.innerHTML = this.val;
      }
    },
    /**
     * Get the content html in the editor
     */
    getContent() {
      let content = "";
      let $content = this.$refs.editorContent;
      if ($content) {
        content = $content.innerHTML;
      }
      return content;
    },
    savePosition() {
      this.setValueIframe();
      this.savedSel = "";
      this.savedSel = this.saveSelection();
      // this.restoreSelection(savedSel);
    },
    setValueIframe() {
      let content = this.getContent();
      this.$emit("onChange", content);
      this.$emit("input", content);
    },
    renderControl(option) {
      switch (option) {
        case "bold":
          return FormatBold;
        case "italic":
          return FormatItalic;
        case "underline":
          return FormatUnderline;
        case "justifyLeft":
          return FormatAlignLeft;
        case "justifyRight":
          return FormatAlignRight;
        case "justifyCenter":
          return FormatAlignCenter;
        case "color":
          return FormatColorText;
        case "orderedList":
          return FormatOrderedList;
        case "unorderedList":
          return FormatUnorderedList;
        case "link":
          return InsertLink;
        case "image":
          return UploadImage;
        default:
          alert(`Type '${option}' do not support`);
          break;
      }
    },
    mouseup() {
      this.onChangeFormat();
    },
    onKeyup() {
      this.setValueIframe();
      var keyCode = event.keyCode;
      if (event.target.textContent.length <= 0) {
        for (let i = 0; i < this.currentFormat.length; i++) {
          let item = this.currentFormat[i];
          item.$el.classList.remove("gte_active");
        }
      }

      if (
        (keyCode == 37) |
        (keyCode == 38) |
        (keyCode == 39) |
        (keyCode == 40)
      ) {
        this.onChangeFormat();
      }
    },
    onChangeFormat() {
      if (this.currentFormat && this.currentFormat.length > 0) {
        for (let index = 0; index < this.currentFormat.length; index++) {
          let item = this.currentFormat[index];
          if (item.type == "color") {
            var selected = window.getSelection().focusNode.parentNode;
            var color = selected.getAttribute("color");

            if (color) {
              this.optionsTypes = this.optionsTypes.map(function(option) {
                if (option.type == "color") {
                  if (option.color) {
                    option.color = color;
                  }
                }
                return option;
              });
            }
          }

          if (document.queryCommandValue(item.type) == "true") {
            if (!item.$el.classList.contains("gte_active")) {
              item.$el.classList.add("gte_active");
            }
          } else {
            item.$el.classList.remove("gte_active");
          }
        }
      }
    },
    insertLink(data) {
      this.restoreSelection(this.savedSel);
      let urlInsert = `<a href="${data.url}" target="${data.target}" title="${data.title}">${data.text}</a>`;
      document.execCommand("insertHTML", false, urlInsert);
    },
    insertImage(imageObj) {
      if (!imageObj || !imageObj.src) {
        console.error(
          "When adding an image, the object requires the src attribute"
        );
        return;
      }
      let src = imageObj.src;
      let alt = imageObj.alt || "";
      let title = imageObj.title || "";

      let baseImg = `<img src="${src}" alt="${alt}" title="${title}" height="" width="" >`;
      document.execCommand("insertHTML", false, baseImg);
    },
    setFormatText(obj) {
      let $content = this.$refs.editorContent;
      $content.focus();
      if (obj.type == "createLink") {
        this.insertLink(obj);
        return;
      } else if (obj.type == "uploadImage") {
        this.$emit("uploadImages", obj.files);
        return;
      }

      /**
       * Tìm ra hành động hiện tại đã active hay chưa?
       */
      let isFormatActive;
      if (this.currentFormat && this.currentFormat.length > 0) {
        for (let i = 0; i < this.currentFormat.length; i++) {
          let item = this.currentFormat[i];
          if (item.type == obj.type) {
            isFormatActive = true;
            break;
          }
        }
      }

      if (!isFormatActive) {
        // add active
        this.currentFormat.push({
          type: obj.type,
          $el: obj.$el
        });
      } else {
        // remove active
        if (this.currentFormat && this.currentFormat.length > 0) {
          for (let i = 0; i < this.currentFormat.length; i++) {
            let item = this.currentFormat[i];
            if (item.type == obj.type) {
              this.currentFormat.splice(i, 1);
              break;
            }
          }
        }
      }

      this.setExecCommand(obj);
    },
    setExecCommand(obj) {
      if (document.queryCommandValue(obj.type) == "false") {
        // if (!obj.$el.classList.contains("gte_active")) {
        //   obj.$el.classList.add("gte_active");
        // }
        document.execCommand(obj.type, false, obj.value);
      } else {
        // obj.$el.classList.remove("gte_active");
        document.execCommand(obj.type, true, obj.value);
      }

      this.setValueIframe();
    },
    saveSelection() {
      if (window.getSelection) {
        let sel = window.getSelection();
        if (sel.getRangeAt && sel.rangeCount) {
          this.ranges = [];
          for (var i = 0, len = sel.rangeCount; i < len; ++i) {
            this.ranges.push(sel.getRangeAt(i));
          }
          return this.ranges;
        }
      } else if (document.selection && document.selection.createRange) {
        return document.selection.createRange();
      }
      return null;
    },
    restoreSelection(savedSel) {
      if (savedSel) {
        if (window.getSelection) {
          let sel = window.getSelection();
          sel.removeAllRanges();
          for (var i = 0, len = savedSel.length; i < len; ++i) {
            sel.addRange(savedSel[i]);
          }
        } else if (document.selection && savedSel.select) {
          savedSel.select();
        }
      }
    },
    getSelectionHtml() {
      var html = "";
      if (typeof window.getSelection != "undefined") {
        var sel = window.getSelection();
        if (sel.rangeCount) {
          var container = document.createElement("div");
          for (var i = 0, len = sel.rangeCount; i < len; ++i) {
            container.appendChild(sel.getRangeAt(i).cloneContents());
          }
          html = container.innerHTML;
        }
      } else if (typeof document.selection != "undefined") {
        if (document.selection.type == "Text") {
          html = document.selection.createRange().htmlText;
        }
      }
      return html;
    }
  }
};
</script>

<style lang="scss">
.gte_editor {
  width: 100%;
  border: solid 1px #f0f3f6;
  .gte_editor-item {
    display: flex;
    flex-flow: row wrap;
    justify-content: flex-start;
    background: #f9fafb;
    border-top-left-radius: 5px;
    border-top-right-radius: 5px;

    .gte_editor-item--sticky {
      position: sticky;
      top: 0;
    }
    .gte_item,
    .gte_item-btn {
      position: relative;
      border: none;
      background: transparent;
      display: inline-flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      width: 34px;
      height: 34px;
      margin: 2px;
      padding: 0;
      border-radius: 5px;
      transition: all 0.25s;
      svg {
        height: 16px;
      }
      &.gte_active {
        color: #3e50af;
        background: #e8ecf0;
      }
      &:hover {
        background: #e8ecf0;
      }
    }
    .gte_item-btn {
      padding: 0;
      margin: 0;
    }
  }
  .gte_editor_content {
    width: 100%;
    min-height: 200px;
    padding: 10px;
    box-sizing: border-box;
    font-size: 1.2rem;
    overflow-y: auto;
    font-size: 15px;
    line-height: 1.4;
    background: #ffffff;
    outline: none;
    border: none;
    border-bottom-left-radius: 5px;
    border-bottom-right-radius: 5px;

    &::-webkit-scrollbar {
      width: 10px;
    }

    &::-webkit-scrollbar-track {
      box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);
    }

    &::-webkit-scrollbar-thumb {
      background-color: rgba(0, 0, 0, 0.1);
      outline: 1px solid rgba(0, 0, 0, 0.1);
    }
  }

  .gte_btn {
    position: relative;
    display: block;
    border: none;
    outline: none;
    border-radius: 3px;
    cursor: pointer;
    padding: 0 16px;
    transition: all 0.3s;
    background: #f0f3f6;
    color: #43484b;

    font-size: 14px;
    font-weight: 600;
    font-style: normal;

    height: 38px;
    text-decoration: none;
    user-select: none;
    white-space: nowrap;

    display: inline-flex;
    justify-content: center;
    align-items: center;

    p {
      margin: 0;
    }

    &:focus {
      outline: none;
    }

    &:hover {
      background: #e8ecf0;
    }

    &.active {
      background: #f0f3f6;
      color: #3e50af;
    }

    &:active {
      background: #dde1f0;
      color: #3e50af;
    }

    &.disable {
      background: #f0f3f6;
      color: #d6dae0;
      cursor: no-drop;
    }

    /* Button primary */
    &.gte_btn-primary {
      background: #3e50af;
      color: #fff;

      &:hover {
        background: #5466c3;
        color: #fff;
      }

      &:active {
        background: #314299;
        color: #fff;
      }

      &.active {
        background: #314299;
        color: #fff;
      }

      &.disable {
        background: #f0f3f6;
        color: #d6dae0;
      }
    }

    /* Button danger and delete */
    &.gte_btn-danger,
    &.gte_btn-delete {
      background: #ff3f34;
      color: #fff;
      font-weight: 400;

      &:hover {
        background: #eb2f06;
        color: #fff;
      }

      &:active {
        background: #eb2f06;
        color: #fff;
      }

      &.active {
        background: #eb2f06;
        color: #fff;
      }

      &.disable {
        background: #ff3f34;
        color: rgba(#ffffff, 0.2);
      }
    }
  }

  .gte_input {
    display: block;
    width: 100%;
    background: transparent;

    outline: none;
    border: 1px solid #d6dae0;
    padding: 8px 15px;
    border-radius: 3px;
    font-size: 15px;
    line-height: 1.3;
    box-sizing: border-box;
    transition: all 0.3s;

    &:focus {
      outline: none;
      border-color: #3e50af;
      // box-shadow: 0 0 5px 2px rgba(162, 162, 162, 0.16)
    }
    &:hover {
      border-color: #85929e;
    }

    &[readonly] {
      user-select: all;
      background: #f0f3f6;

      &:focus {
        border-color: #d6dae0;
        // box-shadow: 0 0 5px 2px rgba(162, 162, 162, 0.16)
      }
      &:hover {
        border-color: #d6dae0;
      }
    }
  }
}
</style>
