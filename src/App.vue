<template>
  <p-header class="header">
    <div class="header_title">Модуль обратная связь</div>
    <div class="header_options"></div>
  </p-header>

  <div class="area">
    <div class="row items-center justify-between area_content">
      <div class="area_header">Ваши модули</div>
    </div>
    <p-separator />
    <div class="row content">
      <p-card
        class="card row justify-center items-center"
        v-if="content.data[0].items.length == 0"
      >
        <div class="card_default">Пока нет модулей</div>
      </p-card>
      <pCard
        class="card"
        v-for="(item, index) in content.data[0].items"
        :key="index"
      >
        <div class="card_header">{{ item.text }}</div>
        <div class="card_info"></div>
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
        </div>
      </pCard>
      <p-card
        @click="moduleDial = !moduleDial"
        class="card row justify-center items-center hoverable"
      >
        <div class="card_default">Добавить модуль</div>
      </p-card>
    </div>
  </div>

  <feedSettingsDialog
    :model="settings"
    :render="select"
    @closeSettings="closeDialog"
  />

  <deleteDialog :model="deleteDial" @closeDelete="closeDialog" />

  <newModuleDialog :model="moduleDial" @closeModule="closeDialog" />
</template>

<script>
import axios from "axios";

import { ref } from "vue";

import pBtn from "./components/pBtn.vue";
import pCard from "./components/pCard.vue";
import pHeader from "./components/pHeader.vue";
// import pDialog from "./components/pDialog.vue";
import pSeparator from "./components/pSeparator.vue";

import deleteDialog from "./components/deleteDialog.vue";
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
    deleteDialog,
    newModuleDialog,
    pSeparator,
  },
  setup() {
    return {
      moduleDial: ref(false),
      settings: ref(false),
      deleteDial: ref(false),
      select: ref({}),
      content: ref(
        JSON.parse(
          '{"result":true,"data":[{"setting":{"button_hello":"Начать отвечать на вопросы","is_notice":true,"button_cancel":"Отмена","end_message":"Спасибо за ответы, они отправлены администратору","user_limit":1,"period":60,"limit_in_period":60,"template_common":"<b>Вопрос:<\\/b> {TITLE}\\r\\n<b>Ответ:<\\/b> {ANSWERS}","template_answer":"Обратная свя\\u0437ь по сообщению: <b>{TITLE}<\\/b>\\r\\n{ANSWER}"},"items":[{"id":1,"text":"Пришлите файл, пожалуйста","confirm":false,"sort":1,"feedback_id":13,"type":2,"data":{"size":1048579,"extensions":"txt"}},{"id":8,"text":"Как твои дела?","confirm":false,"sort":2,"feedback_id":13,"type":1,"data":{"validator":"","message":""}},{"id":1,"text":"Выберите ответ","confirm":false,"sort":3,"feedback_id":13,"type":3,"data":{"is_multiple":false,"options":[{"text":"Ответ 1","sort":0},{"text":"Ответ 2","sort":1},{"text":"Ответ 3","sort":2}]}}]}]}'
        )
      ),
    };
  },
  methods: {
    closeDialog(dialog) {
      this[dialog] = !this[dialog];
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
      sort
    ) {
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
            sort
          )
        )
        .then((response) => {
          console.log(JSON.parse(response.data));
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
        null
      );
    },
    requestUpdateInputText(input_id, text, confirm) {
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
        null
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
        sort
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
        sort
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
      ];

      let currentParams = {
        bot_id: 886,
        feedback_id: 1086,
      };
      for (let i = 0; i < params.length; i++) {
        if (params[i]) {
          currentParams[currentNameParams[i]] = params[i];
        }
      }
      console.log(currentParams);
      return currentParams;
    },
  },
  mounted() {
    console.log(
      JSON.parse(
        '{"result":true,"data":[{"setting":{"button_hello":"Начать отвечать на вопросы","is_notice":true,"button_cancel":"Отмена","end_message":"Спасибо за ответы, они отправлены администратору","user_limit":1,"period":60,"limit_in_period":60,"template_common":"<b>Вопрос:<\\/b> {TITLE}\\r\\n<b>Ответ:<\\/b> {ANSWERS}","template_answer":"Обратная свя\\u0437ь по сообщению: <b>{TITLE}<\\/b>\\r\\n{ANSWER}"},"items":[{"id":1,"text":"Пришлите файл, пожалуйста","confirm":false,"sort":1,"feedback_id":13,"type":2,"data":{"size":1048579,"extensions":"txt"}},{"id":8,"text":"Как твои дела?","confirm":false,"sort":2,"feedback_id":13,"type":1,"data":{"validator":"","message":""}},{"id":1,"text":"Выберите ответ","confirm":false,"sort":3,"feedback_id":13,"type":3,"data":{"is_multiple":false,"options":[{"text":"Ответ 1","sort":0},{"text":"Ответ 2","sort":1},{"text":"Ответ 3","sort":2}]}}]}]}'
      )
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
  min-width: 250px;
  max-width: 25%;
  margin: 5px;
  flex-grow: 1;
  transition: 0.3s background;
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
    font-size: 20px;
    font-weight: 500;
    padding: 14px;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
  }
  &_actions {
    display: flex;
    justify-content: flex-end;
    padding: 8px;
  }
  &_close {
    width: 20px;
    height: 20px;
    background-image: url("./assets/close.svg");
    background-size: cover;
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
@media (max-width: 626px) {
  .area_content {
    justify-content: center;
  }
  .content {
    justify-content: center;
  }
}
</style>
