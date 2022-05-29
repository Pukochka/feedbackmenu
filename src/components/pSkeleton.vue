<template>
  <pCard class="card loading">
    <div class="h_skeleton"></div>
    <div class="p_skeleton"></div>
    <div class="p_skeleton"></div>
  </pCard>
</template>
<script>
import pCard from "./pCard.vue";
export default {
  components: { pCard },
  setup() {
    return {};
  },
  methods: {},
  mounted() {},
  props: {},
};
</script>
<style lang="scss" scoped>
.full {
  min-height: inherit;
}
.card {
  flex-grow: 1;
  transition: 0.3s background;
  position: relative;
  border-radius: 30px;
}
.h_skeleton,
.p_skeleton {
  width: 100%;
  max-width: 95%;
  margin: 8px auto;
  background: #eee;
  background: linear-gradient(110deg, #ececec 8%, #f5f5f5 18%, #ececec 33%);
  border-radius: 5px;
  background-size: 200% 100%;
  animation: 1.5s shine linear infinite;
}
.p_skeleton {
  padding: 10px;
}
.h_skeleton {
  padding: 20px;
}
@keyframes shine {
  to {
    background-position-x: -200%;
  }
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
@media (max-width: 626px) {
  .card {
    width: 100%;
  }
}
</style>
