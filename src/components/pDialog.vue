<template>
  <Teleport to="body">
    <div class="dialog full fullscreen" :class="{ show: model }">
      <div class="dialog_backdrop full" :class="{ show: model }"></div>
      <div
        @click="animate"
        class="dialog_inner full fixed"
        :class="{ show: model, animate: start }"
        :style="{ transition: start ? '.1s transform' : '' }"
      >
        <slot></slot>
      </div>
    </div>
  </Teleport>
</template>
<script>
import { ref } from "vue";
export default {
  props: {
    model: {
      type: Boolean,
      required: false,
    },
  },
  setup() {
    return {
      name: ref(false),
      start: ref(false),
    };
  },
  methods: {
    animate(e) {
      let a = Array.from(e.path);
      a.splice(Array.from(e.path).length - 4, 4);
      if (a.length <= 2) {
        this.start = true;
        setTimeout(() => {
          this.start = false;
        }, 50);
      }
    },
  },
  watch: {},
};
</script>
<style lang="scss" scoped>
.dialog {
  position: absolute;
  transition: 0.3s all;
  visibility: hidden;
  font-family: "Roboto", "sans-serif";
  &.show {
    visibility: visible;
  }

  &_backdrop {
    position: fixed;
    z-index: -1;
    pointer-events: all;
    outline: none;
    opacity: 0;
    transition: 0.3s opacity;
    background: rgba(0, 0, 0, 0.4);
    &.show {
      opacity: 1;
    }
  }

  &_inner {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 24px;
    outline: 0;
    transform: scale(0);
    transition: 0.3s transform;
    &.animate {
      transition: 0.1s transform !important;
      transform: scale(1.01) !important;
    }
    & > div {
      overflow: auto;
      will-change: scroll-position;
      box-shadow: 0 2px 4px -1px #0003, 0 4px 5px #00000024,
        0 1px 10px #0000001f;
      border-radius: 4px;
      pointer-events: all;
    }
    &.show {
      transform: scale(1);
    }
  }
}
.full {
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
.fixed {
  position: fixed;
}
.fullscreen {
  z-index: 6000;
  border-radius: 0 !important;
  max-width: 100vw;
  max-height: 100vh;
}
</style>
