<template>
  <p-dialog :model="model">
    <p-card>
      <div class="header">
        Выберите модуль
        <p-btn color="transparent" @click="closeModule"
          ><div class="close"></div
        ></p-btn>
      </div>
      <!-- <pSeparator /> -->

      <div class="content row">
        <div
          class="content_item"
          v-for="(mod, index) in modules"
          :key="index"
          :class="{ select: mod.select }"
          @click="selectModule(mod)"
        >
          <div class="content_header">{{ mod.header }}</div>
          <div class="content_options">
            <ul>
              <li v-for="(option, index) in mod.options" :key="index">
                {{ option }}
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div class="actions">
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Отмена"
          @click="closeModule"
        />
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Добавить"
          @click="createNewModule"
        />
      </div>
    </p-card>
  </p-dialog>
</template>
<script>
import { ref } from "vue";
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
  emits: ["closeModule", "createNewModule"],
  setup() {
    return {
      moduleConfig: ref(0),
      modules: ref([
        {
          header: "Сообщение",
          options: ["Валидация", "Сообщение об ошибке"],
          select: false,
          type: 1,
        },
        {
          header: "Файл",
          options: ["Установить размер", "Выбрать расширение"],
          select: false,
          type: 2,
        },
        {
          header: "Опрос",
          options: ["Добавление вопросов"],
          select: false,
          type: 3,
        },
      ]),
    };
  },
  methods: {
    closeModule() {
      this.$emit("closeModule", "moduleDial");
      this.modules.forEach((m) => (m.select = false));
    },
    selectModule(item) {
      this.modules.forEach((m) => (m.select = false));
      item.select = !item.select;
      this.moduleConfig = item.type;
    },
    createNewModule() {
      this.closeModule();
      this.$emit("createNewModule", this.moduleConfig);
    },
  },
  mounted() {},
  props: {
    label: {
      type: String,
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
  width: 20px;
  height: 20px;
  background-image: url("../assets/close.svg");
  background-size: cover;
}
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 24px;
  padding: 7px;
  font-weight: 500;
}
.content {
  padding: 10px;
  display: flex;
  &_item {
    padding: 15px;
    margin: 5px;
    border: 2px solid #aaa;
    border-radius: 8px;
    cursor: pointer;
    transition: 0.2s border, 0.2s background;
    flex-grow: 1;
    &.select {
      background: rgba(8, 121, 19, 0.145);
      border: 2px solid rgb(8, 121, 20);
    }
  }
  &_header {
    font-size: 20px;
    font-weight: 500;
    color: #222;
  }
  &_options > ul {
    list-style-type: none;
    padding: 0;
    & > li {
      padding: 4px 8px;
      background: #ddd;
      box-shadow: 0 1px 5px #0003, 0 2px 2px #00000024, 0 3px 1px -2px #0000001f;
      color: #333;
      border-radius: 4px;
      margin: 6px;
      font-size: 14px;
    }
  }
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
