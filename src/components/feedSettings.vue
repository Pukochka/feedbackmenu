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
            label="Изменить название"
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
          <p-btn
            margin="5px 20px"
            label="Подтверждение"
            color="transparent"
            padding="8px 16px"
            @click="renderSettings.confirm = !renderSettings.confirm"
            ><div
              class="check"
              :class="{ checked: renderSettings.confirm }"
            ></div
          ></p-btn>
        </div>
        <div class="card_container_item" v-if="render.type == 2">
          <div class="file_input">
            <p-input
              label="Изменить название"
              v-model="file.value"
              :max="file.max"
            />
            <pInputRequired :model="file" />
          </div>
          <div class="row">
            <div class="file_maxsize full-flex">
              <div class="">
                Максимальный размер файла : {{ size.value }} Mb
              </div>

              <p-slider
                v-model="size.value"
                :maxvalue="size.max"
                :minvalue="size.min"
              />
            </div>
            <div class="file_extensions full-flex">
              Выбранное расширение : {{ selectExtensionValue }}
              <p-select :select="selectExtensionValue">
                <div
                  class="file_extensions_inner"
                  v-for="(ext, index) in extensions"
                  @click="selectExtension(ext)"
                  :key="index"
                >
                  {{ ext.name }}
                </div>
              </p-select>
            </div>
          </div>

          <p-btn
            margin="5px 20px"
            label="Подтверждение"
            color="transparent"
            padding="8px 16px"
            @click="renderSettings.confirm = !renderSettings.confirm"
            ><div
              class="check"
              :class="{ checked: renderSettings.confirm }"
            ></div
          ></p-btn>
        </div>
        <div class="card_container_item" v-if="render.type == 3">
          <div class="answers">
            <div class="answers_input">
              <p-input
                label="Изменить название"
                v-model="answerName.value"
                :max="answerName.max"
              />
              <pInputRequired :model="answerName" />
            </div>
          </div>
          <p-btn
            margin="5px 20px"
            label="Несколько ответов"
            color="transparent"
            padding="8px 16px"
            @click="renderSettings.is_multiple = !renderSettings.is_multiple"
            ><div
              class="check"
              :class="{ checked: renderSettings.is_multiple }"
            ></div
          ></p-btn>
          <p-btn
            margin="5px 20px"
            label="Подтверждение"
            color="transparent"
            padding="8px 16px"
            @click="renderSettings.confirm = !renderSettings.confirm"
            ><div
              class="check"
              :class="{ checked: renderSettings.confirm }"
            ></div
          ></p-btn>

          <div class="answers">
            <div class="row justify-center answers_render_header">
              Все доступные ответы
            </div>
            <div class="answers_render">
              <div
                class="answers_render_button"
                v-for="(item, index) in render.data.options"
                :key="index"
              >
                <div class="answers_render_option row">
                  <p-btn
                    @click="
                      optionsDelete = !optionsDelete;
                      selectDeleteOption = item.text;
                    "
                    padding="3px"
                    color="transparent"
                    class="answers_render_inner"
                    ><div class="answers_render_delete"></div
                  ></p-btn>
                  <p-btn
                    @click="
                      editAnswerDial = !editAnswerDial;
                      editAnswer.value = item.text;
                    "
                    padding="3px"
                    color="transparent"
                    class="answers_render_inner"
                    ><div class="answers_render_edit"></div
                  ></p-btn>
                </div>

                <div class="word">
                  {{ item.text }}
                </div>
              </div>
            </div>
            <div class="answer_area" v-if="newOptionDial">
              <div class="full-flex">
                <p-input
                  label="Новый ответ"
                  v-model="newAnswer.value"
                  :max="newAnswer.max"
                />
                <pInputRequired :model="newAnswer" />
              </div>
              <div class="row">
                <p-btn
                  padding="4px 8px"
                  color="transparent"
                  class=""
                  label="Добавить"
                >
                </p-btn>
                <p-btn
                  padding="4px 8px"
                  color="transparent"
                  class=""
                  label="Отмена"
                  @click="newOptionDial = !newOptionDial"
                >
                </p-btn>
              </div>
            </div>
            <p-btn
              margin="5px 20px"
              v-if="!newOptionDial"
              label="Добавить ответ"
              color="transparent"
              padding="8px 16px"
              @click="newOptionDial = !newOptionDial"
            />
          </div>
        </div>
      </div>
      <p-separator />
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
          label="Удалить"
          @click="optionsDelete = !optionsDelete"
        />
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Сохранить"
          @click="closeSettings"
        />
      </div>
    </pCard>
  </pDownDialog>

  <pDownDialog :model="editAnswerDial">
    <p-card>
      <div class="card_header">Изменение ответа</div>
      <p-separator />
      <div class="card_container_item" style="padding: 20px">
        <p-input
          label="Изменить ответ"
          v-model="editAnswer.value"
          :max="editAnswer.max"
        />
        <pInputRequired :model="editAnswer" />
      </div>

      <p-separator />
      <div class="card_actions">
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Закрыть"
          @click="editAnswerDial = !editAnswerDial"
        />
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Сохранить"
          @click="editAnswerDial = !editAnswerDial"
        />
      </div>
    </p-card>
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
import pDownDialog from "./pDialog.vue";
import pSeparator from "./pSeparator.vue";
import pInput from "./pInput.vue";
import pInputRequired from "./pInputRequired.vue";
import pSelect from "./pSelect.vue";
import pSlider from "./pSlider.vue";

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
    pSelect,
    pSlider,
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
      editAnswerDial: ref(false),
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
        max: 25,
      }),
      file: ref({
        value: "",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 30,
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
        max: 40,
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
        max: 40,
      }),
      editAnswer: ref({
        value: "answer",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 40,
      }),
      renderSettings: ref({
        is_multiple: false,
        confirm: false,
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
        this.renderSettings.confirm = this.render.confirm;
      } else if (this.render.type == 2) {
        this.size.value = this.converterToBites;
        this.file.value = this.render.text;
        this.renderSettings.confirm = this.render.confirm;
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
        this.editAnswer.value = this.render.text;
        this.renderSettings.is_multiple = this.render.is_multiple;
        this.renderSettings.confirm = this.render.confirm;
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
    font-size: 22px;
    padding: 7px;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
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
    padding: 8px;
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
  padding: 6px;
  &_inner {
    padding: 5px;
  }
}
.word {
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
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
  &_render {
    padding: 5px 10px 0 10px;
    flex-grow: 1;
    max-height: 200px;
    overflow-y: scroll;
    &::-webkit-scrollbar {
      width: 6px;
      background: rgb(255, 255, 255);
    }
    &::-webkit-scrollbar-thumb {
      width: 6px;
      background: rgb(159, 157, 157);
      border-radius: 5px;
    }
    &_delete,
    &_edit,
    &_done {
      padding: 3px;
      width: 20px;
      height: 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      background-image: url("../assets/close.svg");
      background-size: cover;
    }
    &_edit {
      background-image: url("../assets/edit.svg");
    }
    &_option {
      position: absolute;
      top: 50%;
      left: 10px;
      transform: translateY(-50%);
    }
    &_done {
      background-image: url("../assets/done.svg");
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
      padding-top: 10px;
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
@media (max-width: 768px) {
  .card {
    width: 80%;
  }
}
@media (max-width: 424px) {
  .card {
    width: 100%;
  }
}
</style>
