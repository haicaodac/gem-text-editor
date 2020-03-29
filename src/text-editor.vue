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
  name: "TextEditor", // vue component name
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
        return {
          options: [
            "bold",
            "italic",
            "underline",
            "justifyLeft",
            "justifyCenter",
            "justifyRight",
            "insertOrderedList",
            "insertUnorderedList",
            "foreColor",
            "createlink",
            "image"
          ]
        };
      }
    },
    maxHeight: {
      type: String, // 100px
      default: "200px"
    },
    minHeight: {
      type: String, // 100px
      default: "200px"
    },
    hide: {
      type: Boolean,
      default() {
        return true;
      }
    },
    value: {
      type: String,
      required: false,
      default() {
        return "";
      }
    },
    readonly: {
      type: Boolean,
      required: false,
      default() {
        return false;
      }
    },
    screens: {
      type: [Array, Object],
      default: () => []
    }
  },
  data() {
    return {
      val: "",
      sel: "",
      savedSel: "",
      ranges: [],

      editorProps: {},
      optionsTypes: [],
      currentFormat: []
    };
  },
  mounted() {
    this.val = this.value;
    let optionsValue = [];
    if (this.init.options && this.init.options.length) {
      for (let index = 0; index < this.init.options.length; index++) {
        let item = this.init.options[index];
        optionsValue[index] = {
          type: item
        };
        if (item == "foreColor") {
          optionsValue[index]["color"] = "#000";
        }
      }
      this.optionsTypes = optionsValue;
    }
    document.execCommand("defaultParagraphSeparator", false, "div");
  },
  methods: {
    savePosition() {
      this.setValueIframe();
      this.savedSel = "";
      this.savedSel = this.saveSelection();
      // this.restoreSelection(savedSel);
    },
    setValueIframe() {
      var divContent = document.createElement("div");
      divContent.className = "gt_box-desc";
      divContent.innerHTML = document.querySelector(
        ".gte_editor_content"
      ).innerHTML;

      this.$emit("onChange", this.id, divContent.outerHTML);
      this.$emit("inpput", divContent.outerHTML);
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
        case "foreColor":
          return FormatColorText;
        case "insertOrderedList":
          return FormatOrderedList;
        case "insertUnorderedList":
          return FormatUnorderedList;
        case "createlink":
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
          if (item.type == "foreColor") {
            var selected = window.getSelection().focusNode.parentNode;
            var color = selected.getAttribute("color");

            if (color) {
              this.optionsTypes = this.optionsTypes.map(function(option) {
                if (option.type == "foreColor") {
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
    clearFormatText() {
      let $content = document.querySelector(".gte_editor_content");
      $content.focus();
      document.execCommand("forecolor", false, "#333333");
      this.setValueIframe();
    },
    setFormatText(obj) {
      let $content = document.querySelector(".gte_editor_content");
      $content.focus();
      if (obj.type == "createLink") {
        this.insertLink(obj);
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

<template>
  <div class="gte_editor">
    <div v-if="optionsTypes && optionsTypes.length" class="gte_editor-item">
      <component
        :is="renderControl(option.type)"
        v-for="option in optionsTypes"
        :key="option.type"
        v-bind="option"
        @statusFormatText="setFormatText"
        @clearFormatText="clearFormatText"
      />
    </div>
    <div
      class="gte_editor_content"
      contenteditable="true"
      @blur="savePosition"
      @keyup="onKeyup"
      @mouseup="mouseup"
      v-html="val"
      :style="{'max-height': maxHeight, 'min-height': minHeight}"
    />
  </div>
</template>

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
    .gte_item,
    .gte_item-btn {
      position: relative;
      border: none;
      background: transparent;
      height: auto;
      padding: 0;
    }
    .gte_item {
      display: inline-flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      width: 34px;
      height: 34px;
      margin: 2px;
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
