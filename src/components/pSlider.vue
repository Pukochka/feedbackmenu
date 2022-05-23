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
  height: 25px;
  -webkit-appearance: none;
  margin: 10px 0;
  width: 95%;
}
input[type="range"]:focus {
  outline: none;
}
input[type="range"]::-webkit-slider-runnable-track {
  width: 100%;
  height: 5px;
  cursor: pointer;
  box-shadow: 0px 0px 0px #000000;
  background: #ccc;
  border-radius: 1px;
  border: 0px solid #000000;
}
input[type="range"]::-webkit-slider-thumb {
  box-shadow: 0px 0px 0px #000000;
  border: 1px solid #ccc;
  height: 18px;
  width: 18px;
  border-radius: 25px;
  background: #a1d0ff;
  cursor: pointer;
  -webkit-appearance: none;
  margin-top: -7px;
}
input[type="range"]:focus::-webkit-slider-runnable-track {
  background: #ccc;
}
input[type="range"]::-moz-range-track {
  width: 100%;
  height: 5px;
  cursor: pointer;
  box-shadow: 0px 0px 0px #000000;
  background: #ccc;
  border-radius: 1px;
  border: 0px solid #000000;
}
input[type="range"]::-moz-range-thumb {
  box-shadow: 0px 0px 0px #000000;
  border: 1px solid #ccc;
  height: 18px;
  width: 18px;
  border-radius: 25px;
  background: #a1d0ff;
  cursor: pointer;
}
input[type="range"]::-ms-track {
  width: 100%;
  height: 5px;
  cursor: pointer;
  background: transparent;
  border-color: transparent;
  color: transparent;
}
input[type="range"]::-ms-fill-lower {
  background: #ccc;
  border: 0px solid #000000;
  border-radius: 2px;
  box-shadow: 0px 0px 0px #000000;
}
input[type="range"]::-ms-fill-upper {
  background: #ccc;
  border: 0px solid #000000;
  border-radius: 2px;
  box-shadow: 0px 0px 0px #000000;
}
input[type="range"]::-ms-thumb {
  margin-top: 1px;
  box-shadow: 0px 0px 0px #000000;
  border: 1px solid #ccc;
  height: 18px;
  width: 18px;
  border-radius: 25px;
  background: #a1d0ff;
  cursor: pointer;
}
input[type="range"]:focus::-ms-fill-lower {
  background: #ccc;
}
input[type="range"]:focus::-ms-fill-upper {
  background: #ccc;
}
@media (max-width: 768px) {
  input[type="range"] {
    width: 80%;
  }
}
</style>