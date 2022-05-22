<template>
  <header class="p-header">
    <div class="full flex items-center justify-between">
      <slot></slot>
    </div>
    <pSeparator />
  </header>
</template>
<script>
import pSeparator from "./pSeparator.vue";
export default {
  components: {
    pSeparator,
  },
  setup() {
    return {};
  },
  methods: {},
  mounted() {},
  props: {
    label: {
      type: String,
      required: false,
    },
  },
};
</script>
<style lang="scss" scoped>
.full {
  min-height: inherit;
}
.p-header {
  min-height: 60px;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 2000;
}
.flex {
  display: flex;
}
.justify {
  &-center {
    justify-content: center;
  }
  &-between {
    justify-content: space-between;
  }
  &-around {
    justify-content: space-around;
  }
  &-start {
    justify-content: flex-start;
  }
  &-end {
    justify-content: flex-end;
  }
}
.no-wrap {
  flex-wrap: nowrap;
}
.row {
  display: flex;
  flex-wrap: wrap;
}
.items {
  &-center {
    align-items: center;
  }
}
</style>
