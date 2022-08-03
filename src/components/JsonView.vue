<!-- The code has been modified -->
<template>
  <div class="json-view-main">
    <div class="block_content">
      <draggable
        :list="flowData"
        item-key="index"
        handle=".dragbar"
        @end="onDragEnd"
      >
        <template #item="{ element, index }">
          <div
            :class="[
              'block',
              'clearfix',
              { 'hide-block': hideMyBlock[index] == true },
            ]"
          >
            <span class="json-key">
              <input
                type="text"
                v-model="element.name"
                class="key-input"
                v-if="typeof element.name == 'string'"
                @blur="keyInputBlur(element, $event)"
              />
              <i
                class="collapse-down v-json-edit-icon-arrow_drop_down"
                v-if="element.type == 'object' || element.type == 'array'"
                @click="closeBlock(index, $event)"
              ></i>
              <i v-if="element.type == 'object'" class="i-type">{{
                "{" + element.childParams.length + "}"
              }}</i>
              <i v-if="element.type == 'array'" class="i-type">{{
                "[" + element.childParams.length + "]"
              }}</i>
            </span>
            <span class="json-val">
              <template v-if="element.type == 'object'">
                <json-view
                  v-model:parsedData="element.childParams"
                  @update:parsed-data="onChange"
                ></json-view>
              </template>

              <template v-else-if="element.type == 'array'">
                <array-view
                  v-model:parsedData="element.childParams"
                  @update:parsed-data="onChange"
                ></array-view>
              </template>

              <template v-else>
                <span class="val">
                  <input
                    type="text"
                    v-model="element.remark"
                    class="val-input"
                    v-if="element.type == 'string'"
                  />
                  <input
                    type="number"
                    v-model.number="element.remark"
                    class="val-input"
                    v-if="element.type == 'number'"
                    @input="numberInputChange(element)"
                  />
                  <select
                    name="value"
                    v-model="element.remark"
                    class="val-input"
                    v-if="element.type == 'boolean'"
                  >
                    <option :value="true">true</option>
                    <option :value="false">false</option>
                  </select>
                </span>
              </template>
            </span>

            <div class="tools">
              <select
                v-model="element.type"
                class="tools-types"
                @change="itemTypeChange(element)"
              >
                <option
                  v-for="(type, index) in formats"
                  :value="type"
                  :key="index"
                >
                  {{ type }}
                </option>
              </select>
              <i class="dragbar v-json-edit-icon-drag"></i>
              <i class="del-btn" @click="delItem(parsedData, element, index)">
                <i class="v-json-edit-icon-huishouzhan_huaban"></i>
              </i>
            </div>
          </div>
        </template>
      </draggable>

      <item-add-form
        v-if="toAddItem"
        @confirm="newItem"
        @cancel="cancelNewItem"
      ></item-add-form>

      <div class="block add-key" @click="addItem" v-if="!toAddItem">
        <i class="v-json-edit-icon-add"></i>
      </div>
    </div>
  </div>
</template>

<script setup>
import ItemAddForm from "./ItemAddForm.vue";
import { computed, ref } from "vue";
import arrayView from "./ArrayView.vue";
import draggable from "vuedraggable";

const props = defineProps(["parsedData"]);
const emit = defineEmits(["update:parsedData"]);
const formats = ["string", "array", "object", "number", "boolean"];
const flowData = computed(() => {
  return props.parsedData;
});
const toAddItem = ref(false);
const hideMyBlock = ref({});

function onChange() {
  emit("update:parsedData", flowData.value);
}
function delItem(parentDom, item, index) {
  flowData.value.splice(index, 1);

  if (hideMyBlock.value[index]) hideMyBlock.value[index] = false;

  onChange();
}
function closeBlock(index, e) {
  hideMyBlock.value[index] = hideMyBlock.value[index] ? false : true;
}
function addItem() {
  toAddItem.value = true;
}
function cancelNewItem() {
  toAddItem.value = false;
}
function newItem(obj) {
  let oj = {
    name: obj.key,
    type: obj.type,
  };
  if (obj.type == "array" || obj.type == "object") {
    oj.childParams = obj.val;
    oj.remark = null;
  } else {
    oj.childParams = null;
    oj.remark = obj.val;
  }
  if (!oj.name) {
    alert("please must input a name!");
    return;
  } else {
    flowData.value.push(oj);
    onChange();
    cancelNewItem();
  }
}
function keyInputBlur(item, e) {
  if (item.name.length <= 0) {
    alert("please must input a name!");
    item.name = "null";
    e.target.focus();
  }
}
function onDragEnd() {
  onChange();
}
function itemTypeChange(item) {
  if (item.type === "array" || item.type === "object") {
    item.childParams = [];
    item.remark = null;
  }
  if (item.type === "boolean") {
    item.remark = true;
  }
  if (item.type === "string") {
    item.remark = "";
  }
  if (item.type === "number") {
    item.remark = 0;
  }
}
function numberInputChange(item) {
  if (!item.remark) item.remark = 0;
}
</script>
<style lang="scss" scoped>
@import "../assets/styles/common.scss";
</style>
