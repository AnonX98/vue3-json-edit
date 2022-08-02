<template>
  <div class="add-form pure-form">
    <div class="f-input">
      <input
        type="text"
        v-model="keyName"
        v-if="props.needName"
        class="f-input-m"
        placeholder="name"
      />
      <select v-model="formatSelected" class="f-input-m">
        <option v-for="(item, index) in formats" :value="item" :key="index">
          {{ item }}
        </option>
      </select>
      <span class="f-input-m">
        <b>:</b>
      </span>

      <template v-if="formatSelected != 'array' && formatSelected != 'object'">
        <input
          type="text"
          v-model="valName"
          class="f-input-m"
          placeholder="value"
          v-if="formatSelected == 'string'"
        />
        <input
          type="number"
          v-model="valName"
          class="f-input-m"
          placeholder="value"
          v-if="formatSelected == 'number'"
          @change="dealNumber"
        />
        <select
          name="value"
          v-model="valName"
          class="f-input-m"
          v-if="formatSelected == 'boolean'"
          @change="dealBoolean"
        >
          <option :value="true">true</option>
          <option :value="false">false</option>
        </select>
      </template>
    </div>

    <div class="f-btns">
      <button class="pure-button f-confirm" @click="confirm">
        {{ formBtnText.confirmText }}
      </button>
      <button class="pure-button" @click="cancel">
        {{ formBtnText.cancelText }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, inject } from "vue";

const props = defineProps({
  needName: {
    default: true,
  },
});
const emit = defineEmits(["confirm", "cancel"]);

const formats = ["string", "array", "object", "number", "boolean"];
const formatSelected = ref("string");
const keyName = ref("");
const valName = ref("");

const formBtnText = inject("formBtnText");

function confirm() {
  let val = null;
  if (formatSelected.value === "array" || formatSelected.value === "object") {
    val = [];
  } else {
    val = valName.value;
  }
  let objData = {
    key: props.needName ? keyName.value : null,
    val: val,
    type: formatSelected.value,
  };
  emit("confirm", objData);
  keyName.value = "";
  valName.value = "";
  formatSelected.value = "string";
}
function cancel() {
  emit("cancel");
}
function dealBoolean() {
  valName.value = Boolean(valName);
}
function dealNumber() {
  valName.value = Number(valName);
}
</script>

<style lang="scss" scoped>
.pure-button {
  font-family: inherit;
  font-size: 100%;
  padding: 0.5em 1em;
  color: rgba(0, 0, 0, 0.8);
  border: none transparent;
  background-color: #e6e6e6;
  text-decoration: none;
  border-radius: 2px;
}
.pure-button {
  display: inline-block;
  line-height: normal;
  white-space: nowrap;
  vertical-align: middle;
  text-align: center;
  cursor: pointer;
  -webkit-user-drag: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}

.f-input,
.f-btns {
  display: inline-block;
}
.f-btns {
  display: inline-block;
  margin-top: 0.5em;
}
.f-confirm {
  color: #fff;
  background: #05a5d1;
}
.add-form {
  margin-bottom: 20px;
  font-size: 0.8rem;
}
</style>
