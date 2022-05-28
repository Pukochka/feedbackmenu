<template>
  <pDownDialog :model="model">
    <pCard class="card">
      <div class="card_header flex justify-between items-center">
        <div class="row items-center">
          <p>Настройки обратной связи</p>
        </div>

        <div class="row items-center">
          <pSpinner :model="spinner" />
          <p-btn color="transparent" @click="closeSettings"
            ><div class="card_close"></div
          ></p-btn>
        </div>
      </div>
      <pSeparator />
      <div class="card_container">
        <div class="status" v-if="!spinner">
          Все изменения успешно сохранены
        </div>
        <div class="status pro" v-if="spinner">
          Изменение в процессе сохранение
        </div>
        <div class="card_container_item" v-if="render.type == 1">
          <p-input
            label="Изменить название"
            v-model="text.value"
            :max="text.max"
          />
          <pInputRequired :model="text" />
        </div>
        <div class="card_container_item" v-if="render.type == 1">
          <div class="card_container_item_inner">
            <div class="card_container_item_inner">
              <div class="">Правило валидации</div>
              <p-select :select="selectValidatorsValue">
                <div
                  class="file_extensions_item"
                  v-for="(valid, index) in validations"
                  @click="selectValidators(valid)"
                  :key="index"
                >
                  {{ valid.name }}
                </div>
              </p-select>
            </div>
          </div>
        </div>
        <div
          class="card_container_item"
          v-if="render.type == 1 && selectValidatorsEdit == null"
        >
          <p-input
            label="
              Валидатор, здесь записывается регулярное выражение!
            "
            v-model="editValidator.value"
            :max="editValidator.max"
          />
          <pInputRequired :model="editValidator" />
        </div>
        <div
          class="card_container_item"
          v-if="render.type == 1 && (selectValidatorsEdit == true) | null"
        >
          <p-input
            label="Сообщение, если неверно"
            v-model="message.value"
            :max="message.max"
          />
          <pInputRequired :model="message" />
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
              Выбранное расширение : {{ selectExtensionValue.join(", ") }}
              <p-select multiple :select="selectExtensionValue.join(', ')">
                <div
                  class="file_extensions_inner"
                  v-for="(ext, index) in extensions"
                  @click="selectExtension(ext)"
                  :key="index"
                >
                  <div class="file_extensions_item">
                    <div class="check" :class="{ checked: ext.select }"></div>
                    {{ ext.name }}
                  </div>
                </div>
              </p-select>
            </div>
          </div>
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

          <div class="answers">
            <div class="row answers_render_header">Все доступные ответы</div>
            <div class="answers_render">
              <div
                class="answers_render_button"
                v-if="render.data.options.length == 0"
              >
                Пока что нет ответов!
              </div>
              <TransitionGroup name="list" tag="div">
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
                        selectOptionId = item.id;
                      "
                      title="Удалить"
                      padding="3px"
                      color="transparent"
                      class="answers_render_inner"
                      ><div class="answers_render_delete"></div
                    ></p-btn>
                    <p-btn
                      @click="
                        editAnswerDial = !editAnswerDial;
                        editAnswer.value = item.text;
                        selectOptionId = item.id;
                      "
                      title="Изменить"
                      padding="3px"
                      color="transparent"
                      class="answers_render_inner"
                      ><div class="answers_render_edit"></div
                    ></p-btn>
                  </div>

                  <div class="word">
                    {{ item.text }}
                  </div>

                  <div class="answers_render_option_right row">
                    <p-btn
                      @click="optionUp(item.id)"
                      v-if="index > 0"
                      padding="3px"
                      title="Поднять"
                      color="transparent"
                      class="answers_render_inner"
                      ><div class="answers_render_up"></div
                    ></p-btn>
                    <p-btn
                      @click="optionDown(item.id)"
                      v-if="index + 1 < render.data.options.length"
                      padding="3px"
                      color="transparent"
                      title="Опустить"
                      class="answers_render_inner"
                      ><div class="answers_render_down"></div
                    ></p-btn>
                  </div>
                </div>
              </TransitionGroup>
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
              <div class="row inner">
                <p-btn
                  padding="4px 8px"
                  color="transparent"
                  class=""
                  label="Добавить"
                  @click="addNewOption"
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
              v-if="!newOptionDial && render.data.options.length != 10"
              label="Добавить ответ"
              color="transparent"
              padding="8px 16px"
              @click="newOptionDial = !newOptionDial"
            />
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
          </div>
        </div>
        <div class="card_container_item">
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
          @click="moduleDelete = !moduleDelete"
        />
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Сохранить"
          @click="
            closeSettings();
            saveSettings();
          "
        />
      </div>
    </pCard>
  </pDownDialog>

  <pDownDialog :model="editAnswerDial">
    <p-card class="card min">
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
          @click="editOption"
        />
      </div>
    </p-card>
  </pDownDialog>

  <pDownDialog :model="optionsDelete">
    <p-card class="card min">
      <div class="card_container">
        Вы уверены что хотите удалить <b>{{ selectDeleteOption }}</b> ?
      </div>
      <div class="card_actions">
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Отмена"
          @click="optionsDelete = !optionsDelete"
        />
        <p-btn
          padding="4px 16px"
          color="transparent"
          label="Удалить"
          @click="deleteOption"
        />
      </div>
    </p-card>
  </pDownDialog>

  <deleteDialog
    name="moduleDelete"
    :input_id="render"
    :model="moduleDelete"
    :label="selectDeleteModule"
    @closeDelete="closeDialog"
    @deleteModule="deleteModule"
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
import pSpinner from "./pSpinner.vue";

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
    pSpinner,
  },
  emits: [
    "closeSettings",
    "deleteModule",
    "saveTextInput",
    "saveFileInput",
    "saveSelectInput",
    "addNewOption",
    "deleteOption",
    "editOption",
    "optionDown",
    "optionUp",
  ],
  props: {
    model: {
      type: Boolean,
      required: false,
    },
    spinner: {
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
      content: ref([]),
      newOptionDial: ref(false),
      optionsDelete: ref(false),
      moduleDelete: ref(false),
      editAnswerDial: ref(false),
      selectDeleteOption: ref(""),
      selectDeleteModule: ref(""),
      selectOptionId: ref(0),
      selectExtensionValue: ref([]),
      selectValidatorsValue: ref(""),
      selectValidatorsValidate: ref(""),
      selectValidatorsEdit: ref(true),
      validations: ref([
        {
          name: "Нет",
          validate: "",
          default: "",
          edit: false,
        },
        {
          name: "Своe (Рекомендуются необходимые знания)",
          validate: "",
          default: "",
          edit: null,
        },
        {
          name: "Номер телефона",
          validate: "/^(s*)?(+)?([- _():=+]?d[- _():=+]?){10,14}(s*)?$/",
          default: "Номер телефона указан не верно",
          edit: true,
        },
        {
          name: "Почта",
          validate:
            "/^((([0-9A-Za-z]{1}[-0-9A-z.]{1,}[0-9A-Za-z]{1})|([0-9А-Яа-я]{1}[-0-9А-я.]{1,}[0-9А-Яа-я]{1}))@([-0-9A-Za-z]{1,}.){1,2}[-A-Za-z]{2,})$/u",
          default: "Почта указана не верно",
          edit: true,
        },
        {
          name: "Ссылка",
          validate: "/^(https?://)?([w-]{1,32}.[w-]{1,32})[^s@]*$/",
          default: "Ссылка указана не верно",
          edit: true,
        },
      ]),
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
        max: 40,
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
        max: 40,
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
      editValidator: ref({
        value: "",
        required() {
          if (this.value.length > this.max || this.value.length < this.min) {
            return false;
          } else {
            return true;
          }
        },
        min: 5,
        max: 200,
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
      size: ref({
        value: 1,
        max: 100,
        min: 0.01,
      }),
      renderSettings: ref({
        is_multiple: false,
        confirm: false,
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
        {
          select: false,
          name: "gif",
        },
      ]),
    };
  },
  methods: {
    closeDialog(name) {
      this[name] = !this[name];
    },
    closeSettings() {
      this.$emit("closeSettings", "settings");
    },
    deleteModule(id, type) {
      this.closeSettings();
      this.$emit("deleteModule", id, type);
    },
    deleteOption() {
      this.optionsDelete = !this.optionsDelete;
      this.$emit("deleteOption", this.selectOptionId);
    },
    editOption() {
      this.editAnswerDial = !this.editAnswerDial;
      this.$emit("editOption", this.selectOptionId, this.editAnswer.value);
    },
    optionUp(id) {
      this.$emit("optionUp", id);
    },
    optionDown(id) {
      this.$emit("optionDown", id);
    },
    selectExtension(item) {
      if (item.select) {
        if (this.selectExtensionValue.length == 1) {
          return;
        }
        item.select = false;
        this.selectExtensionValue = this.selectExtensionValue.filter(
          (ext) => ext != item.name
        );
      } else {
        item.select = true;
        this.selectExtensionValue.push(item.name);
      }
    },
    selectValidators(item) {
      this.selectValidatorsValue = item.name;
      this.message.value = item.default;
      this.selectValidatorsValidate = item.validate;
      this.selectValidatorsEdit = item.edit;
    },
    addNewOption() {
      this.newOptionDial = false;
      this.$emit("addNewOption", this.render.id, this.newAnswer.value);
      this.newAnswer.value = "";
    },
    saveSettings() {
      if (this.render.type == 1) {
        this.$emit(
          "saveTextInput",
          this.render.id,
          this.text.value,
          this.renderSettings.confirm,
          this.selectValidatorsValidate,
          this.message.value
        );
      } else if (this.render.type == 2) {
        this.$emit(
          "saveFileInput",
          this.render.id,
          this.file.value,
          this.renderSettings.confirm,
          this.size.value * 10e5,
          this.selectExtensionValue.join(", ")
        );
      } else if (this.render.type == 3) {
        this.$emit(
          "saveSelectInput",
          this.render.id,
          this.answerName.value,
          this.renderSettings.confirm,
          this.renderSettings.is_multiple
        );
      }
      // else if(this.render.type == 4){

      // }else if(this.render.type == 5){

      // }
    },
  },
  watch: {
    render() {
      console.log(this.render);
    },
    model() {
      if (this.render.type == 1) {
        this.selectDeleteModule = this.text.value = this.render.text;
        this.renderSettings.confirm = this.render.confirm;
        this.selectValidatorsValue = this.validations.filter(
          (valid) => valid.validate == this.render.data.validator
        )[0].name;
        this.message.value = this.render.data.message;

        if (this.selectValidatorsValue == undefined) {
          this.selectValidatorsValue = this.validations[1].name;
        }
      } else if (this.render.type == 2) {
        this.size.value = this.converterToBites;
        this.selectDeleteModule = this.file.value = this.render.text;
        this.renderSettings.confirm = this.render.confirm;
        this.selectExtensionValue = this.convertToArray;
        this.extensions.forEach((ext) => {
          ext.select = false;
          this.selectExtensionValue.forEach((val) => {
            if (val == ext.name) {
              ext.select = true;
            }
          });
        });
      } else if (this.render.type == 3) {
        this.selectDeleteModule = this.answerName.value = this.render.text;
        this.editAnswer.value = this.render.text;
        this.renderSettings = {
          is_multiple: this.render.data.is_multiple,
          confirm: this.render.confirm,
        };
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
    convertToArray() {
      let a = this.render.data.extensions.replace(/,/gi, "");
      let myindex = 0;
      let arr = [];
      let str = "";
      for (let l of [...a]) {
        if (l == " ") {
          myindex++;
          str = "";
        } else {
          str += l;
          arr[myindex] = str;
        }
      }
      return arr;
    },
  },
  mounted() {},
};
</script>

<style lang="scss" scoped>
.card {
  width: 50%;
  &.min {
    width: 35%;
  }
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
      &_inner {
        padding: 0px 20px;
      }
    }
  }
  &_actions {
    display: flex;
    justify-content: flex-end;
    padding: 8px;
  }
}
.status {
  font-size: 14px;
  font-weight: 400;
  color: green;
  &.pro {
    color: darkgoldenrod;
  }
}
.flex {
  display: flex;
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
  &_item {
    padding: 4px 0;
    transition: 0.3s background;
    position: relative;
    &.checked {
      border-color: #33af50;
      background-color: #80ed99;
    }
    &:hover {
      background: rgba(0, 0, 0, 0.14);
    }
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
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
.answers {
  &_render {
    padding: 5px 10px 0 10px;
    flex-grow: 1;
    max-height: 200px;
    overflow-y: scroll;
    overflow-x: hidden;
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
    &_done,
    &_up,
    &_down {
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
    &_up {
      background-image: url("../assets/arrowUp.svg");
    }
    &_down {
      background-image: url("../assets/arrowDown.svg");
    }
    &_option {
      position: absolute;
      top: 50%;
      left: 10px;
      transform: translateY(-50%);
      &_right {
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
        right: 10px;
      }
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
      background: rgba(0, 0, 0, 0.049);
    }
    &_header {
      padding-top: 10px;
      font-size: 16px;
      padding: 0 20px;
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
.inner {
  padding: 0 30px;
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
