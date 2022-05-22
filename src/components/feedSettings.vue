<template>
  <pDownDialog :model="model">
    <pCard class="card">
      <div class="card_header row justify-between">
        Настройки обратной связи
        <p-btn color="transparent" @click="closeSettings"
          ><div class="card_close"></div
        ></p-btn>
      </div>
      <pSeparator />
      <div class="card_container">
        <div class="card_container_item" v-if="render.type == 1">
          <p-input
            label="Правило валидации"
            v-model="text.value"
            :max="text.max"
          />
          <pInputRequired :model="text" />
        </div>
        <div class="card_container_item" v-if="render.type == 1">
          <p-input
            label="Правило валидации"
            v-model="validator.value"
            :max="validator.max"
          />
          <pInputRequired :model="validator" />
        </div>
        <div class="card_container_item" v-if="render.type == 1">
          <p-input
            label="Сообщение, если неверно"
            v-model="message.value"
            :max="message.max"
          />
          <pInputRequired :model="message" />
        </div>
        <div class="card_container_item" v-if="render.type == 2">
          <div class="file_maxsize">
            <div class="file_extensions_inner">
              Максимальный размер файла : {{ size.value }} Mb
            </div>

            <input
              type="range"
              v-model="size.value"
              :max="size.max"
              :min="size.min"
              step="0.01"
            />
          </div>
          <div class="file_extensions">
            Выбранное расширение : {{ selectExtensionValue }}
            <div class="row file_extensions_inner">
              <p-btn
                color="transparent"
                v-for="(ext, index) in extensions"
                :key="index"
                :label="ext.name"
                @click="selectExtension(ext)"
                :border="ext.select ? '2px solid green' : '2px solid grey'"
              />
            </div>
          </div>
        </div>
        <div class="card_container_item" v-if="render.type == 3">
          <div class="answers">
            <div class="answers_input">
              <p-input
                label="Название"
                v-model="answerName.value"
                :max="answerName.max"
              />
              <pInputRequired :model="answerName" />
            </div>

            <div class="answers_multiple">
              <p-btn
                label="Несколько ответов"
                color="transparent"
                padding="8px 16px"
                @click="
                  renderSettings.is_multiple = !renderSettings.is_multiple
                "
                ><div
                  class="check"
                  :class="{ checked: renderSettings.is_multiple }"
                ></div
              ></p-btn>
            </div>
          </div>

          <div class="answers">
            <div class="answers_render">
              <div class="row justify-center answers_render_header">
                Все доступные ответы
              </div>

              <div
                class="answers_render_button"
                v-for="(item, index) in render.data.options"
                :key="index"
              >
                <p-btn
                  @click="
                    optionsDelete = !optionsDelete;
                    selectDeleteOption = item.text;
                  "
                  padding="0"
                  color="transparent"
                  class="answers_render_inner"
                  ><div class="answers_render_delete"></div
                ></p-btn>
                {{ item.text }}
              </div>
              <div class="answer_area row" v-if="newOptionDial">
                <p-btn
                  margin="5px"
                  padding="10px"
                  color="transparent"
                  class=""
                  title="Добавить"
                >
                  <div class="answers_render_done"></div>
                </p-btn>
                <div class="full-flex">
                  <p-input
                    label="Новый ответ"
                    v-model="newAnswer.value"
                    :max="newAnswer.max"
                  />
                  <pInputRequired :model="newAnswer" />
                </div>

                <p-btn
                  margin="5px"
                  padding="10px"
                  color="transparent"
                  class=""
                  title="Отмена"
                  @click="newOptionDial = !newOptionDial"
                >
                  <div class="answers_render_delete"></div>
                </p-btn>
              </div>
              <p-btn
                v-if="!newOptionDial"
                label="Добавить ответ"
                color="transparent"
                padding="8px 16px"
                @click="newOptionDial = !newOptionDial"
              />
            </div>
          </div>
        </div>
        <div class="card_actions">
          <p-btn
            padding="4px 16px"
            color="transparent"
            label="Закрыть"
            @click="closeSettings"
          />
          <p-btn
            padding="4px 16px"
            color="transparent"
            label="Сохранить"
            @click="closeSettings"
          />
        </div>
      </div>
    </pCard>
  </pDownDialog>
  <deleteDialog
    :model="optionsDelete"
    @closeDelete="closeDialog"
    :label="selectDeleteOption"
  />
</template>

<script>
import { ref } from "vue";

import pBtn from "./pBtn.vue";
import pCard from "./pCard.vue";
import pDownDialog from "./pDownDialog.vue";
import pSeparator from "./pSeparator.vue";
import pInput from "./pInput.vue";
import pInputRequired from "./pInputRequired.vue";

import deleteDialog from "./deleteDialog.vue";

export default {
  name: "App",
  components: {
    pBtn,
    pDownDialog,
    pCard,
    pSeparator,
    pInput,
    pInputRequired,
    deleteDialog,
  },
  emits: ["closeSettings"],
  props: {
    model: {
      type: Boolean,
      required: false,
    },
    render: {
      type: Object,
      required: false,
    },
  },
  setup() {
    return {
      newOptionDial: ref(false),
      optionsDelete: ref(false),
      selectDeleteOption: ref(""),
      selectExtensionValue: ref(""),
      text: ref({
        value: "",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 20,
      }),
      validator: ref({
        value: "",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 20,
      }),
      message: ref({
        value: "",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 20,
      }),
      answerName: ref({
        value: "",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 20,
      }),
      newAnswer: ref({
        value: "",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 20,
      }),
      renderSettings: ref({
        is_multiple: false,
      }),
      size: ref({
        value: 1,
        max: 100,
        min: 0.01,
      }),
      danger: ref({
        extension: false,
        size: false,
      }),
      extensions: ref([
        {
          select: false,
          name: "txt",
        },
        {
          select: false,
          name: "jpg",
        },
        {
          select: false,
          name: "docx",
        },
        {
          select: false,
          name: "pptx",
        },
        {
          select: false,
          name: "png",
        },
      ]),
    };
  },
  methods: {
    closeDialog() {
      this.optionsDelete = !this.optionsDelete;
    },
    closeSettings() {
      this.$emit("closeSettings", "settings");
    },
    selectExtension(item) {
      this.extensions.forEach((ext) => (ext.select = false));
      item.select = true;
      this.selectExtensionValue = item.name;
    },
  },
  watch: {
    model() {
      if (this.render.type == 1) {
        this.text.value = this.render.text;
        this.message.value = this.render.data.message;
        this.validator.value = this.render.data.validator;
      } else if (this.render.type == 2) {
        this.size.value = this.converterToBites;
        this.selectExtensionValue = this.extensions.filter(
          (ext) => ext.name == this.render.data.extensions
        )[0].name;
        this.extensions.forEach((ext) => {
          ext.select = false;
          if (ext.name == this.render.data.extensions) {
            ext.select = true;
          }
        });
      } else if (this.render.type == 3) {
        this.answerName.value = this.render.text;
        this.renderSettings.is_multiple = this.render.is_multiple;
      }
      // else if(this.render.type == 4){

      // }else if(this.render.type == 5){

      // }
    },
  },
  computed: {
    converterToBites() {
      return (this.render.data.size / 1e6).toFixed(2);
    },
  },
  mounted() {},
};
</script>

<style lang="scss" scoped>
.card {
  width: 50%;
  &_header {
    font-size: 24px;
    padding: 7px;
    font-weight: 500;
  }
  &_close {
    width: 20px;
    height: 20px;
    background-image: url("../assets/close.svg");
    background-size: cover;
  }
  &_container {
    padding: 7px 7px 0 7px;
    &_item {
      padding-right: 7px;
    }
  }
  &_actions {
    display: flex;
    justify-content: flex-end;
    padding: 4px;
  }
}
.actions {
  &_item {
    font-size: 14px;
    font-weight: 300;
    padding: 7px 0;
    transition: 0.3s background;
    &:hover {
      background: rgb(237, 237, 237);
    }
  }
}
.file_maxsize,
.file_extensions {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 15px;
  &_inner {
    padding: 5px;
  }
}

.danger {
  font-size: 11px;
  color: red;
}
.full-flex {
  flex-grow: 1;
}
input[type="range"]::-webkit-slider-runnable-track {
  cursor: grabbing;
}
.answers {
  display: flex;
  align-items: center;
  &_render {
    padding: 25px 10px 0 10px;
    flex-grow: 1;
    &_inner {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      left: 10px;
    }
    &_delete {
      width: 20px;
      height: 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      background-image: url("../assets/close.svg");
      background-size: cover;
    }
    &_done {
      width: 20px;
      height: 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      background-image: url("../assets/done.svg");
      background-size: cover;
    }
    &_button {
      position: relative;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 8px 16px;
      border-radius: 4px;
      margin: 3px;
      transition: 0.3s background;
      &:hover {
        background: rgba(0, 0, 0, 0.049);
      }
    }
    &_header {
      font-size: 14px;
    }
  }
  &_multiple {
    flex-grow: 1;
    margin: 3px;
  }
  &_input {
    flex-grow: 1;
    margin: 3px;
  }
}
.check {
  width: 14px;
  height: 14px;
  border: 2px solid #aaa;
  border-radius: 4px;
  position: absolute;
  top: 50%;
  left: 10px;
  transform: translateY(-50%);
  transition: 0.3s background-color;
  &.checked {
    border-color: #33af50;
    background-color: #80ed99;
  }
}
.row {
  display: flex;
  flex-wrap: wrap;
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
