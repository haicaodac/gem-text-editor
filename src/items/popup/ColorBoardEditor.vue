<template>
  <div class="gte_popup_color">
    <div v-if="code_colors && code_colors.length" class="gte_popup_color-set">
      <span
        v-for="color in code_colors"
        :key="color.color_code"
        class="gte_popup_color-select-color"
        :data-color="color.color_code"
        :style="{ background: color.color_code }"
        :class="[color.status ? 'gte_active' : '']"
        :title="color.color_code"
        @click="setColor(color)"
      >
        <span class="gte_popup_color-outline" />
        <span class="gte_popup_color-check">
          <svg v-if="color.color_code == 'Clear Color'" viewBox="0 0 512 512">
            <g class="fa-group">
              <path
                fill="currentColor"
                d="M512 428v40a12 12 0 0 1-12 12H144a48 48 0 0 1-33.94-14.06l-96-96a48 48 0 0 1 0-67.88l136-136 227.88 227.88L355.88 416H500a12 12 0 0 1 12 12z"
                class="fa-secondary"
              />
              <path
                fill="currentColor"
                d="M377.94 393.94l120-120a48 48 0 0 0 0-67.88l-160-160a48 48 0 0 0-67.88 0l-120 120 45.25 45.25z"
                class="fa-primary"
              />
            </g>
          </svg>
        </span>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "ColorBoard",
  props: {
    colors: {
      type: [Array, Object],
      required: false,
      default: () => [
        {
          color_code: "#61BD6D",
          status: false
        },
        {
          color_code: "#1ABC9C",
          status: false
        },
        {
          color_code: "#54ACD2",
          status: false
        },
        {
          color_code: "#2C82C9",
          status: false
        },
        {
          color_code: "#9365B8",
          status: false
        },
        {
          color_code: "#475577",
          status: false
        },
        {
          color_code: "#CCCCCC",
          status: false
        },
        {
          color_code: "#41A85F",
          status: false
        },
        {
          color_code: "#00A885",
          status: false
        },
        {
          color_code: "#3D8EB9",
          status: false
        },
        {
          color_code: "#2969B0",
          status: false
        },
        {
          color_code: "#553982",
          status: false
        },
        {
          color_code: "#28324E",
          status: false
        },
        {
          color_code: "#000000",
          status: false
        },
        {
          color_code: "#F7DA64",
          status: false
        },
        {
          color_code: "#FBA026",
          status: false
        },
        {
          color_code: "#EB6B56",
          status: false
        },
        {
          color_code: "#E25041",
          status: false
        },
        {
          color_code: "#A38F84",
          status: false
        },
        {
          color_code: "#EFEFEF",
          status: false
        },
        {
          color_code: "#FFFFFF",
          status: false
        },
        {
          color_code: "#FAC51C",
          status: false
        },
        {
          color_code: "#F37934",
          status: false
        },
        {
          color_code: "#D14841",
          status: false
        },
        {
          color_code: "#B8312F",
          status: false
        },
        {
          color_code: "#7C706B",
          status: false
        },
        {
          color_code: "#D1D5D8",
          status: false
        },
        {
          color_code: "Clear Color",
          status: false
        }
      ]
    },
    color: String,
    classButton: String,
    type: String
  },
  data() {
    return {
      code_colors: []
    };
  },
  watch: {
    color(newVal) {
      this.code_colors = this.code_colors.map(function(item) {
        if (item.color_code == newVal.toUpperCase()) {
          item.status = true;
        } else {
          item.status = false;
        }
        return item;
      });
    }
  },
  created() {
    let _this = this;
    let colors = this.colors.map(function(color) {
      if (color.color_code == _this.$props.color) {
        color.status = true;
      }
      return color;
    });

    this.code_colors = colors;
  },
  mounted() {
    document.addEventListener("click", this.checkClosePopup, false);
  },
  beforeDestroy() {
    document.removeEventListener("click", this.checkClosePopup, false);
  },
  methods: {
    checkClosePopup() {
      let $target = event.target;
      if (
        $target &&
        ($target.closest(".gte_popup_color") ||
          $target.closest('[data-type="color"]'))
      ) {
        return;
      }
      this.$emit("close");
    },
    setColor(color) {
      let newColor = "";
      let $editor = event.target.closest(".gte_editor");
      if ($editor) {
        let $editContent = $editor.querySelector(".gte_editor_content");
        if ($editContent) {
          const style = getComputedStyle($editContent);
          newColor = style.color;
        }
      }
      if (color.color_code != "Clear Color") {
        newColor = color.color_code;
      }
      this.$emit("setColorTextBoard", newColor);
    }
  }
};
</script>
<style lang="scss">
.gte_popup_color {
  // display: none;
  position: absolute;
  top: calc(100% + 9px);
  color: #222222;
  background: #fff;
  border-radius: 3px;
  box-shadow: 0px 7px 64px rgba(0, 0, 0, 0.15);
  font-family: Arial, Helvetica, sans-serif;
  box-sizing: border-box;
  user-select: none;
  z-index: 2147483635;
  text-align: left;
  background-clip: padding-box;
  text-rendering: optimizelegibility;
  line-height: 1.2;

  &::after {
    content: "";
    position: absolute;
    top: -16px;
    left: 50%;
    -webkit-transform: translateX(-50%);
    transform: translateX(-50%);
    border: solid 8px #ffffff;
    border-right: 8px solid transparent;
    border-left: 8px solid transparent;
    border-top: 8px solid transparent;
  }

  .gte_popup_color-set {
    padding: 8px;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    width: 175px;
    -webkit-box-orient: horizontal;
    -webkit-box-direction: normal;
    -ms-flex-flow: row wrap;
    flex-flow: row wrap;
    -ms-flex-pack: distribute;
    justify-content: space-around;
    cursor: default;
    .gte_popup_color-select-color.gte_active {
      .gte_popup_color-check {
        svg {
          display: block !important;
        }
      }
    }
    .gte_popup_color-select-color:hover {
      .gte_popup_color-outline {
        position: absolute;
        width: 93%;
        height: 93%;
        border: 1px solid;
      }
    }
    .gte_popup_color-select-color {
      width: 25px;
      height: 25px;
      position: relative;
      cursor: pointer;

      .gte_popup_color-check {
        width: 25px;
        height: 25px;
        display: flex;
        justify-content: center;
        align-items: center;
        svg {
          .fa-secondary {
            fill: currentColor;
            opacity: 0.4;
          }
        }
      }
    }
  }
}
</style>
