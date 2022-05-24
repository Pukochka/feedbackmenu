<template>
  <p-dialog :model="model">
    <p-card>
      <div class="content">
        Вы уверены что хотите удалить <b>{{ label }}</b> ?
      </div>
      <div class="actions">
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Отмена"
          @click="closeDelete"
        />
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Удалить"
          @click="deleteModule"
        />
      </div>
    </p-card>
  </p-dialog>
</template>
<script>
// import pSeparator from "./pSeparator.vue";
import pCard from "./pCard.vue";
import pDialog from "./pDialog.vue";
import pBtn from "./pBtn.vue";
export default {
  components: {
    // pSeparator,
    pDialog,
    pCard,
    pBtn,
  },
  emits: ["closeDelete", "deleteModule"],
  setup() {
    return {};
  },
  methods: {
    closeDelete() {
      this.$emit("closeDelete", this.name);
    },
    deleteModule() {
      this.closeDelete();
      this.$emit("deleteModule", this.input_id.id, this.input_id.type);
    },
  },
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
.close {
  position: absolute;
  top: 7px;
  right: 7px;
  width: 20px;
  height: 20px;
  background-image: url("../assets/close.svg");
  background-size: cover;
}
.content {
  padding: 10px;
}
.actions {
  display: flex;
  justify-content: flex-end;
  padding: 8px;
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
