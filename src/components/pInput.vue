<template>
  <div class="inner">
    <div class="label" @click="$refs.input.focus()">
      <div class="label_text" :class="{ active: isActive }">{{ label }}</div>
    </div>
    <input
      @focus="styleActive"
      @blur="styleDisActive"
      class="input"
      ref="input"
      :type="type"
      :style="{ borderBottom: `1px solid ${bordercolor};` }"
      :value="modelValue"
      :maxlength="+max"
      @input="$emit('update:modelValue', $event.target.value)"
    />
  </div>
</template>
<script>
import { ref } from "vue";
export default {
  setup() {
    return {
      name: ref(""),
      isActive: ref(false),
      isBlur: ref(true),
    };
  },
  mounted() {
    if (this.modelValue.length != 0) {
      this.isActive = true;
    } else {
      this.isActive = false;
    }
  },
  watch: {
    modelValue(value) {
      if (this.isBlur) {
        if (value.length != 0) {
          this.isActive = true;
        } else {
          this.isActive = false;
        }
      }
    },
  },
  methods: {
    styleActive() {
      this.isActive = true;
      this.isBlur = false;
    },
    styleDisActive() {
      this.isBlur = true;
      if (this.modelValue.length != 0) {
        this.isActive = true;
      } else {
        this.isActive = false;
      }
    },
  },
  props: {
    max: {
      type: Number,
      required: false,
    },
    label: {
      type: String,
      required: false,
    },
    text: {
      type: String,
      required: false,
    },
    type: {
      type: String,
      required: false,
      default: "text",
    },
    modelValue: {
      type: String,
      required: false,
    },
    color: {
      type: String,
      required: false,
    },
    textcolor: {
      type: String,
      required: false,
    },
    size: {
      type: String,
      required: false,
    },
    bordercolor: {
      type: String,
      required: false,
    },
  },
};
</script>
<style lang="scss" scoped>
.input {
  border: none;
  outline: none;
  height: 24px;
  width: 100%;
  font-size: 15px;
  margin: 4px;
  border-bottom: 1px solid #aaa;
  z-index: 1;
}
.label {
  display: flex;
  justify-content: flex-start;
  position: relative;
  &_text {
    font-weight: 500;
    transition: 0.3s transform;
    transform: translateY(18px) scale(1);

    &.active {
      transform: translateX(-15px) translateY(6px) scale(0.8);
    }
  }
}
.inner {
  padding: 0 30px;
}
</style>
