<template>
  <input
    type="range"
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
    :min="minvalue"
    :max="maxvalue"
    step="0.01"
  />
</template>
<script>
// import { ref } from "vue";
export default {
  props: {
    minvalue: {
      type: Number,
      reqired: false,
    },
    maxvalue: {
      type: Number,
      reqired: false,
    },
    modelValue: {
      type: String,
      required: false,
    },
  },
  setup() {
    return {};
  },
};
</script>
<style lang="scss" scoped>
input[type="range"] {
  overflow: hidden;
  width: 95%;
  -webkit-appearance: none;
  background-color: #00e1ff;
  border-radius: 4px;
}

input[type="range"]::-webkit-slider-runnable-track {
  height: 10px;
  -webkit-appearance: none;
  color: #ccc;
  margin-top: -1px;
}

input[type="range"]::-webkit-slider-thumb {
  width: 20px;
  -webkit-appearance: none;
  height: 10px;
  border-radius: 4px;
  cursor: ew-resize;
  background: #434343;
  box-shadow: 1000px 0 0 1000px #ccc;
}

input[type="range"]::-moz-range-progress {
  background-color: #ccc;
}
input[type="range"]::-moz-range-track {
  background-color: #00e1ff;
}
input[type="range"]::-ms-fill-lower {
  background-color: #ccc;
}
input[type="range"]::-ms-fill-upper {
  background-color: #00e1ff;
}
@media (max-width: 768px) {
  input[type="range"] {
    width: 80%;
  }
}
</style>