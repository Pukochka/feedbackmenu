<template>
  <small class="info" :class="{ danger: !this.model.required() }"
    >{{ this.model.value.length }} / {{ this.model.max }}</small
  >
</template>
<script>
// import { ref } from "vue";
export default {
  props: {
    model: {
      type: Object,
      reqired: false,
    },
  },
  setup() {
    return {};
  },
};
</script>
<style lang="scss" scoped>
.info {
  display: flex;
  justify-content: flex-end;
  font-size: 11px;
}
.danger {
  width: 100%;
  font-size: 11px;
  color: red;
}
</style>