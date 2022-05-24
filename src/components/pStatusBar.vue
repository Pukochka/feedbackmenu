<template>
  <Teleport to="body">
    <div class="status">
      <div class="status_bar">
        <div class="status_item">Модуль файл успешно добавлен</div>
        <div class="status_item">Модуль Сообщения успешно удален</div>
        <div class="status_item">fff</div>
        <div class="status_item">fff</div>
      </div>
    </div>
  </Teleport>
</template>
<script>
// import pSeparator from "./pSeparator.vue";
// import pCard from "./pCard.vue";
// import pBtn from "./pBtn.vue";
export default {
  components: {
    // pSeparator,
    // pCard,
    // pBtn,
  },
  emits: [],
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
    name: {
      type: String,
      required: false,
    },
    input_id: {
      type: Object,
      required: false,
    },
    model: {
      type: Boolean,
      required: false,
    },
  },
};
</script>
<style lang="scss" scoped>
.status {
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  position: fixed;
  &_bar {
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    position: absolute;
    width: 200px;
    height: 90%;
    border-radius: 4px;
    bottom: 20px;
    right: 20px;
  }
  &_item {
    padding: 5px 20px;
    font-size: 14px;
    font-weight: 500;
    width: 100%;
    min-height: 40px;
    background: rgba(0, 255, 0, 0.14);
    margin: 3px;
    border-radius: 4px;
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
</style>
