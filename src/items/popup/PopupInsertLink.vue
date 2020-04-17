<template>
  <div class="gte_popup_link">
    <form method="POST" action @submit="submit">
      <div class="gte_popup_link_form-group">
        <label>Url</label>
        <input class="gte_input" type="text" v-model="valUrl" placeholder="Url" />
      </div>
      <div class="gte_popup_link_form-group">
        <label>Text</label>
        <input class="gte_input" type="text" v-model="valTitle" />
      </div>
      <template v-if="!showAdvanced">
        <span class="gte_popup_link_advanced" @click="showAdvanced = true">
          <svg viewBox="0 0 384 512">
            <path
              fill="currentColor"
              d="M376 232H216V72c0-4.42-3.58-8-8-8h-32c-4.42 0-8 3.58-8 8v160H8c-4.42 0-8 3.58-8 8v32c0 4.42 3.58 8 8 8h160v160c0 4.42 3.58 8 8 8h32c4.42 0 8-3.58 8-8V280h160c4.42 0 8-3.58 8-8v-32c0-4.42-3.58-8-8-8z"
            />
          </svg>
          Advanced Settings
        </span>
      </template>
      <template v-if="showAdvanced">
        <span class="gte_popup_link_advanced" @click="showAdvanced = false">
          <svg viewBox="0 0 384 512">
            <path
              fill="currentColor"
              d="M376 232H8c-4.42 0-8 3.58-8 8v32c0 4.42 3.58 8 8 8h368c4.42 0 8-3.58 8-8v-32c0-4.42-3.58-8-8-8z"
              class
            />
          </svg>
          Advanced Settings
        </span>
      </template>
      <template v-if="showAdvanced">
        <div class="gte_popup_link_form-group">
          <label>Title</label>
          <input class="gte_input" type="text" v-model="valTitle" />
        </div>
        <div class="gte_popup_link_form-group">
          <label>Open link in...</label>
          <select v-model="valTarget" class="gte_input">
            <option value>Current window</option>
            <option value="_blank">New window</option>
          </select>
        </div>
      </template>
      <div class="gte_popup_link_list_button">
        <button
          type="button"
          class="gte_btn gte_popup_link_button_cancle_insert_link"
          @click="cancleInsertLink"
        >Cancel</button>
        <button
          type="submit"
          class="gte_btn gte_btn-primary gte_popup_link_button_insert_link"
        >Insert</button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  props: {
    valueUrl: {
      type: String,
      default: ""
    },
    valueTitle: {
      type: String,
      default: ""
    },
    valueText: {
      type: String,
      default: ""
    },
    valueTarget: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      valUrl: "",
      valTitle: "",
      valTarget: "",
      valText: "",
      showAdvanced: false
    };
  },
  mounted() {
    this.valUrl = this.valueUrl;
    this.valTitle = this.valueTitle;
    this.valText = this.valueText;
    this.valTarget = this.valueTarget;

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
        ($target.closest(".gte_popup_link") ||
          $target.closest('[data-type="link"]'))
      ) {
        return;
      }
      this.$emit("close");
    },
    cancleInsertLink() {
      event.preventDefault();

      this.$emit("close");
    },
    submit() {
      event.preventDefault();
      var data = {
        title: this.valTitle,
        text: this.valText,
        url: this.valUrl,
        target: this.valTarget
      };
      this.$emit("submitFormInsertLink", data);
      return false;
    }
  }
};
</script>
<style lang="scss" scoped>
.gte_popup_link {
  background: #ffffff;
  border-radius: 10px;
  position: absolute;
  top: calc(100% + 9px);
  width: 240px;
  box-shadow: 0px 7px 64px rgba(0, 0, 0, 0.15);
  padding: 16px 24px;
  cursor: default;
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
  .gte_popup_link_advanced {
    display: flex;
    align-items: center;
    // justify-content: center;
    font-size: 12px;
    cursor: pointer;
    user-select: none;
    margin-bottom: 6px;
    svg {
      height: 0.9em !important;
      margin-right: 0.2rem;
    }
    &:hover {
      color: #3e50af;
    }
  }
  .gte_popup_link_form-group {
    margin-bottom: 12px;
    label {
      font-size: 13px;
      color: #43484b;
      margin-bottom: 5px;
    }
  }
}
.gte_popup_link_list_button {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
  .gte_popup_link_button_insert_link {
    margin-left: 8px;
  }
}
</style>
