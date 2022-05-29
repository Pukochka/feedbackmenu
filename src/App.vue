<template>
  <p-header class="header">
    <div class="header_title">Модуль обратная связь</div>
    <div class="header_options"><pSpinner :model="requestWaiting" /></div>
  </p-header>

  <div class="area">
    <div class="row items-center justify-between area_content">
      <div class="area_header">Ваши модули</div>
      <div class="row items-center">
        <p-btn
          v-if="!requestWaiting && content.length < 10"
          size="16px"
          padding="4px 8px"
          color="transparent"
          @click="moduleDial = !moduleDial"
          label="Добавить модуль"
        >
        </p-btn>
      </div>
    </div>
    <p-separator />

    <div class="row content">
      <pSkeleton
        v-show="requestWaiting && content.length == 0"
        v-for="(item, index) in 5"
        :key="index"
      />

      <p-card
        class="card full-width row justify-center items-center"
        v-if="!requestWaiting && content.length == 0"
      >
        <div class="card_default">Пока нет модулей</div>
      </p-card>
      <pCard class="card" v-for="(item, index) in content" :key="index">
        <div class="card_header">{{ item.text }}</div>

        <div class="card_index_item">{{ index + 1 }}</div>

        <div class="card_info">
          Тип <b>{{ currentType(item.type) }}</b>
        </div>
        <div class="card_info" v-if="item.type == 1 && item.data.message != ''">
          <b>{{ currentSettings(item.data.message) }}</b>
        </div>
        <div class="card_info" v-if="item.type == 2">
          Расширения : <b>{{ currentSettings(item.data.extensions) }}</b>
        </div>
        <div class="card_info" v-if="item.type == 2">
          Максимальный размер :
          <b>{{ (item.data.size / 10e5).toFixed(2) }} Mb</b>
        </div>
        <div class="card_info" v-if="item.type == 3">
          <div class="row">
            <div
              class=""
              v-if="item.data.options.length == 0"
              style="font-size: 14px"
            >
              Пока нет ответов
            </div>
            <div
              class="card_answer"
              v-else
              v-for="(opt, index) in currentCountOptions(item.data.options)"
              :key="index"
            >
              {{ opt.text }}
            </div>
          </div>
        </div>

        <div class="card_actions">
          <p-btn
            padding="4px 16px"
            color="transparent"
            label="Настроить"
            @click="
              settings = !settings;
              select = item;
            "
          />
          <p-btn
            @click="requestModuleUp(item.sort)"
            v-if="index > 0"
            padding="4px 4pxpx"
            color="transparent"
            title="Поднять"
            ><div class="arrow_up"></div
          ></p-btn>
          <p-btn
            @click="requestModuleDown(item.sort)"
            v-if="index + 1 != content.length"
            padding="4px 4pxpx"
            color="transparent"
            title="Опустить"
            ><div class="arrow_down"></div
          ></p-btn>
        </div>
      </pCard>
      <p-card
        v-if="!requestWaiting && content.length < 10"
        @click="moduleDial = !moduleDial"
        class="card full-width row justify-center items-center hoverable"
      >
        <div class="card_default">Добавить модуль</div>
      </p-card>
    </div>
  </div>

  <feedSettingsDialog
    :model="settings"
    :render="select"
    :spinner="requestWaiting"
    @closeSettings="closeDialog"
    @deleteModule="requestDeleteInput"
    @saveTextInput="requestUpdateInputText"
    @saveFileInput="requestUpdateInputFile"
    @saveSelectInput="requestUpdateInputSelect"
    @addNewOption="requestAddNewOption"
    @deleteOption="requestDeleteOption"
    @editOption="requestUpdateOption"
    @optionDown="requestOptionDown"
    @optionUp="requestOptionUp"
  />

  <newModuleDialog
    :model="moduleDial"
    @closeModule="closeDialog"
    @createNewModule="requestAddNewInput"
  />
</template>

<script>
import axios from "axios";

import { ref } from "vue";

import pBtn from "./components/pBtn.vue";
import pCard from "./components/pCard.vue";
import pHeader from "./components/pHeader.vue";
import pSeparator from "./components/pSeparator.vue";
import pSpinner from "./components/pSpinner.vue";
import pSkeleton from "./components/pSkeleton.vue";

import newModuleDialog from "./components/createNewModule.vue";
import feedSettingsDialog from "./components/feedSettings.vue";

export default {
  name: "App",
  components: {
    pBtn,
    feedSettingsDialog,
    // pDialog,
    pCard,
    pHeader,
    newModuleDialog,
    pSeparator,
    pSpinner,
    pSkeleton,
  },
  setup() {
    return {
      moduleDial: ref(false),
      settings: ref(false),
      deleteDial: ref(false),
      requestWaiting: ref(false),
      select: ref({}),
      content: ref([]),
    };
  },
  watch: {
    content() {
      this.select = this.content.filter((item) => item.id == this.select.id)[0];
      if (this.select == undefined) {
        this.select = {};
      }
    },
  },
  methods: {
    closeDialog(dialog) {
      this[dialog] = !this[dialog];
    },
    currentType(type) {
      if (type == 1) {
        return "сообщение";
      } else if (type == 2) {
        return "файл";
      } else if (type == 3) {
        return "опрос";
      }
      // else if(type == 4){
      //   return 'Сообщение'
      // }else if(type == 5){
      //   return 'Сообщение'
      // }
    },
    currentSettings(item) {
      return [...(item + "")].splice(0, 10).join("") + "...";
    },
    currentCountOptions(options) {
      let currentIndexes = 2;
      if (options.length == 1) {
        currentIndexes--;
      }
      let arr = [];
      for (let i = 0; i < currentIndexes; i++) {
        arr.push(options[i]);
      }
      if (options.length > 2) {
        arr.push({ text: "..." });
      }

      return arr;
    },
    getUserData(
      request,
      input_id,
      type,
      text,
      confirm,
      size,
      extensions,
      is_multiple,
      option_id,
      select_id,
      sort,
      validator,
      message
    ) {
      this.requestWaiting = true;
      axios
        .post(
          `https://api.bot-t.ru/v1/bot/message/feedback/${request}?token=1172473489:AAFoRo3JvyXS5c1l5aW5qvOtDzZEQVJvf0w`,
          this.createPostParams(
            input_id,
            type,
            text,
            confirm,
            size,
            extensions,
            is_multiple,
            option_id,
            select_id,
            sort,
            validator,
            message
          )
        )
        .then((response) => {
          if (response.status == 200) {
            this.requestWaiting = false;
            this.content = [];
            console.log(JSON.parse(response.data));
            for (let item of JSON.parse(response.data).data[0].items) {
              this.content.push(item);
            }
          }
          console.log(this.content);
        });
    },
    requestAddNewInput(type) {
      this.getUserData(
        "create-input",
        null,
        type,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null
      );
    },
    requestDeleteInput(input_id, type) {
      this.getUserData(
        "delete-input",
        input_id,
        type,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null
      );
    },
    requestUpdateInputText(input_id, text, confirm, validator, message) {
      this.getUserData(
        "update-input-text",
        input_id,
        null,
        text,
        confirm,
        null,
        null,
        null,
        null,
        null,
        null,
        validator,
        message
      );
    },
    requestUpdateInputFile(input_id, text, confirm, size, extensions) {
      this.getUserData(
        "update-input-file",
        input_id,
        null,
        text,
        confirm,
        size,
        extensions,
        null,
        null,
        null,
        null,
        null,
        null
      );
    },
    requestUpdateInputSelect(input_id, text, confirm, is_multiple) {
      this.getUserData(
        "update-input-select",
        input_id,
        null,
        text,
        confirm,
        null,
        null,
        is_multiple,
        null,
        null,
        null,
        null,
        null
      );
    },
    requestAddNewOption(select_id, text) {
      this.getUserData(
        "add-select-option",
        null,
        null,
        text,
        null,
        null,
        null,
        null,
        null,
        select_id,
        null,
        null,
        null
      );
    },
    requestUpdateOption(option_id, text) {
      this.getUserData(
        "update-select-option",
        null,
        null,
        text,
        null,
        null,
        null,
        null,
        option_id,
        null,
        null,
        null,
        null
      );
    },
    requestDeleteOption(option_id) {
      this.getUserData(
        "delete-select-option",
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        option_id,
        null,
        null,
        null,
        null
      );
    },
    requestModuleUp(sort) {
      this.getUserData(
        "sort-up",
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        sort,
        null,
        null
      );
    },
    requestModuleDown(sort) {
      this.getUserData(
        "sort-down",
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        sort,
        null,
        null
      );
    },
    requestOptionDown(option_id) {
      this.getUserData(
        "select-option-down",
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        option_id,
        null,
        null,
        null,
        null
      );
    },
    requestOptionUp(option_id) {
      this.getUserData(
        "select-option-up",
        null,
        null,
        null,
        null,
        null,
        null,
        null,
        option_id,
        null,
        null,
        null,
        null
      );
    },

    createPostParams(...params) {
      let currentNameParams = [
        "input_id",
        "type",
        "text",
        "confirm",
        "size",
        "extensions",
        "is_multiple",
        "option_id",
        "select_id",
        "sort",
        "validator",
        "message",
      ];

      let currentParams = {
        bot_id: 886,
        feedback_id: 1086,
      };
      for (let i = 0; i < params.length; i++) {
        if (params[i] !== null) {
          currentParams[currentNameParams[i]] = params[i];
        }
      }
      console.log(currentParams);
      return currentParams;
    },
  },
  mounted() {
    this.getUserData(
      "view",
      null,
      null,
      null,
      null,
      null,
      null,
      null,
      null,
      null,
      null,
      null,
      null
    );
  },
};
</script>

<style lang="scss" scoped>
.area {
  padding: 65px;
  &_header {
    font-size: 22px;
    font-weight: 500;
  }
  &_content {
    padding: 8px;
  }
}
.content {
  padding: 10px;
}
.header {
  background: #0000001a;
  backdrop-filter: blur(7px);
  &_title {
    padding: 10px;
    font-size: 20px;
    font-weight: 500;
  }
  &_options {
    display: flex;
    padding: 10px;
  }
}
.card {
  width: 100%;
  max-width: 1000px;
  margin: 5px auto;
  flex-grow: 1;
  transition: 0.3s background;
  position: relative;
  border-radius: 30px;
  &_answer {
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    padding: 2px 8px;
    margin: 3px;
    border-radius: 4px;
    background: #aaa;
    font-size: 14px;
    font-weight: 400;
  }
  &.hoverable {
    cursor: pointer;
    font-size: 20px;
    &:hover {
      background: rgba(0, 0, 0, 0.136);
    }
  }
  &_default {
    font-weight: 500;
    padding: 20px;
  }
  &_header {
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    font-size: 20px;
    font-weight: 500;
    padding: 14px 20px;

    z-index: 2;
  }
  &_actions {
    display: flex;
    justify-content: flex-end;
    padding: 8px;
  }
  &_index {
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    position: absolute;
    z-index: 1;
    &_item {
      position: absolute;
      top: 30px;
      right: 50px;
      background: #fff;
      border-radius: 20px;
      font-weight: 700;
      font-size: 40px;
      width: 45px;
      height: 45px;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #aaa;
    }
  }
  &_info {
    width: 100%;
    max-width: 95%;
    margin: 8px auto;
    background: #eee;
    font-size: 14px;
    background: #eee;
    color: #444;
    padding: 3px 0;
    padding-left: 30px;

    border-radius: 5px;
  }
  &_close {
    width: 20px;
    height: 20px;
    background-image: url("./assets/close.svg");
    background-size: cover;
  }
}
.arrow {
  &_up {
    width: 20px;
    height: 20px;
    background-image: url("./assets/arrowUp.svg");
    background-size: cover;
  }
  &_down {
    width: 20px;
    height: 20px;
    background-image: url("./assets/arrowDown.svg");
    background-size: cover;
  }
}
.row {
  display: flex;
  flex-wrap: wrap;
}
.list-move, /* apply transition to moving elements */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
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
  .area_content {
    justify-content: center;
  }
  .content {
    justify-content: center;
  }
  .card {
    width: 100%;
  }
}
@media (max-width: 500px) {
  .area {
    padding: 65px 10px;
  }
}
@media (max-width: 400px) {
  .card_index_item {
    right: 10px;
    top: 35px;
    width: 40px;
    height: 40px;
  }
}
</style>
