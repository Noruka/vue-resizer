<template>
  <div
    class="resize_row"
    :style="{
      height: reHeight + 'px',
      width: width,
      maxHeight: maxHeight + 'px',
      minHeight: minHeight + 'px'
    }"
  >
    <div class="resize_row_body">
      <slot></slot>
    </div>
    <div
      class="slider_row"
      @touchstart.passive="mobileResizeRow"
      @mousedown="resizeRow"
      :style="{
        height: sliderWidth + 'px',
        zIndex: sliderZIndex
      }"
    ></div>
  </div>
</template>
<script>
export default {
  name: "ResizeRow",
  props: {
    sliderWidth: {
      type: Number,
      default: 20,
    },
    sliderZIndex: {
      type: Number,
      default: 100,
    },
    height: {
      type: Number,
      default: 400,
    },
    maxHeight: {
      type: Number
    },
    minHeight: {
      type: Number
    },
    width: {
      type: String,
      default: "400px",
    },
    sliderColor: {
      type: String,
      default: "#ffffff",
    },
    sliderBgColor: {
      type: String,
      default: "#ffffff",
    },
    sliderHoverColor: {
      type: String,
      default: "#d8af46",
    },
    sliderBgHoverColor: {
      type: String,
      default: "#d8af46",
    },
  },
  data() {
    return {
      reHeight: this.height,
      reMax: this.maxHeight,
      reMin: this.minHeight,
      isDragging: false,
    };
  },
  methods: {
    mobileResizeRow(e) {
      document.body.style.overflow = "hidden";
      document.body.style.overscrollBehaviorY = "contain";
      e = e || window.event;
      e.stopPropagation();
      let oldPos = e.changedTouches[0].clientY;
      let oldHeight = this.reHeight;
      let newPos = 0;
      let newHeight = 0;
      const vue = this;
      this.isDragging = true;
      this.$emit("isDragging", this.isDragging);
      document.ontouchmove = sliderDrag;
      document.ontouchend = cancelSliderDrag;
      function sliderDrag(e) {
        if (this.time && Date.now() - this.time < 40) return;
        this.time = Date.now();
        e = e || window.event;
        e.stopPropagation();
        newPos = e.changedTouches[0].clientY;
        const movingDistance = oldPos - newPos;
        newHeight = parseInt(oldHeight - movingDistance);
        vue.reHeight = vue.checkMaxMinLength(newHeight);
        vue.$emit("dragging", vue.reHeight);
      }
      function cancelSliderDrag() {
        vue.isDragging = false;
        vue.$emit("isDragging", vue.isDragging);
        document.body.style.overflow = "";
        document.body.style.overscrollBehaviorY = "";
        document.ontouchmove = null;
        document.ontouchend = null;
      }
    },
    resizeRow(e) {
      e = e || window.event;
      e.preventDefault();
      e.stopPropagation();
      let oldPos = e.clientY;
      let oldHeight = this.reHeight;
      let newPos = 0;
      let newHeight = 0;
      const vue = this;
      this.isDragging = true;
      this.$emit("isDragging", this.isDragging);
      document.onmousemove = sliderDrag;
      document.onmouseup = cancelSliderDrag;
      function sliderDrag(e) {
        if (this.time && Date.now() - this.time < 40) return;
        this.time = Date.now();
        e = e || window.event;
        e.preventDefault();
        e.stopPropagation();
        newPos = e.clientY;
        const movingDistance = oldPos - newPos;
        newHeight = parseInt(oldHeight - movingDistance);
        vue.reHeight = vue.checkMaxMinLength(newHeight);
        vue.$emit("dragging", vue.reHeight);
      }
      function cancelSliderDrag() {
        vue.isDragging = false;
        vue.$emit("isDragging", vue.isDragging);
        document.onmouseup = null;
        document.onmousemove = null;
      }
    },
    checkMaxMinLength(newValue) {
      const vue = this
      if (vue.reMin && vue.reMin >= newValue) {
        newValue = vue.reMin
      }
      if (vue.reMax && vue.reMax <= newValue) {
        newValue = vue.reMax
      }
      return newValue
    }
  },
};
</script>
<style>
.resize_row {
  overflow: hidden;
  box-sizing: border-box;
  padding-bottom: 20px;
  position: relative;
}
.resize_row * {
  box-sizing: border-box;
}
.resize_row_body {
  width: 100%;
  height: 100%;
}
.resize_row > .slider_row {
  transition: background 0.2s;
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  z-index: 1;
  cursor: row-resize;
  background-color: #d8af46;
}
.resize_row > .slider_row:before {
  transition: background-color 0.2s;
  position: absolute;
  left: 50%;
  top: 31%;
  transform: translateX(-50%);
  content: "";
  display: block;
  height: 1px;
  width: 24%;
  min-width: 30px;
  max-width: 70px;
  background-color: #ffffff;
}
.resize_row > .slider_row:after {
  transition: background-color 0.2s;
  position: absolute;
  left: 50%;
  bottom: 31%;
  transform: translateX(-50%);
  content: "";
  display: block;
  height: 1px;
  width: 24%;
  min-width: 30px;
  max-width: 70px;
  background-color: #ffffff;
}
.resize_row > .slider_row:hover:before,
.resize_row > .slider_row:hover:after,
.resize_row > .slider_row:active:before,
.resize_row > .slider_row:active:after {
  background-color: #ffffff;
}
.resize_row > .slider_row:hover,
.resize_row > .slider_row:active {
  background: #d8af46;
}
</style>
