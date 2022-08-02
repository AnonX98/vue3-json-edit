<template>
  <div class="json-view-main">
    <div class="block_content array">
      <div class="array-ol">
        <draggable
          :list="flowData"
          item-key="index"
          handle=".handle"
          @end="onDragEnd"
        >
          <template #item="{ element, index }">
            <div
              :class="[
                'array-item',
                { 'hide-item': hideMyItem[index] == true },
              ]"
            >
              <span
                v-if="element.type !== 'object' && element.type !== 'array'"
              >
                <input
                  type="text"
                  v-model="element.remark"
                  class="val-input"
                  v-if="element.type === 'string'"
                  placeholder="string"
                />
                <input
                  type="number"
                  v-model.number="element.remark"
                  class="val-input"
                  v-if="element.type == 'number'"
                  placeholder="number"
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
              <span v-else>
                <span :class="['json-key', 'json-desc']">
                  {{ element.type.toUpperCase() }}
                  <i
                    class="collapse-down v-json-edit-icon-arrow_drop_down"
                    v-if="element.type == 'object' || element.type == 'array'"
                    @click="closeBlock(index, $event)"
                  ></i>
                  <i v-if="element.type == 'object'">{{
                    "{" + element.childParams.length + "}"
                  }}</i>
                  <i v-if="element.type == 'array'">{{
                    "[" + element.childParams.length + "]"
                  }}</i>
                </span>

                <span class="json-val">
                  <template v-if="element.type == 'array'">
                    <array-view
                      v-model:parsedData="element.childParams"
                      @update:parsed-data="onChange"
                    ></array-view>
                  </template>

                  <template v-if="element.type == 'object'">
                    <json-view
                      v-model:parsedData="element.childParams"
                      @update:parsed-data="onChange"
                    ></json-view>
                  </template>
                </span>
              </span>

              <span class="tools">
                <select
                  v-model="element.type"
                  class="tools-types"
                  @change="itemTypeChange(element)"
                >
                  <option
                    v-for="(item, index) in formats"
                    :value="item"
                    :key="index"
                  >
                    {{ item }}
                  </option>
                </select>
                <i class="dragbar v-json-edit-icon-drag handle"></i>
                <i
                  class="del-btn"
                  @click="delItem(props.parsedData, element, index)"
                >
                  <i class="v-json-edit-icon-huishouzhan_huaban"></i>
                </i>
              </span>
            </div>
          </template>
        </draggable>
      </div>

      <item-add-form
        v-if="toAddItem"
        @confirm="newItem"
        @cancel="cancelNewItem"
        :needName="false"
      ></item-add-form>

      <div class="block add-key" v-if="!toAddItem" @click="addItem">
        <i class="v-json-edit-icon-add"></i>
      </div>
    </div>
  </div>
</template>

<script setup>
import ItemAddForm from "./ItemAddForm.vue";
import jsonView from "./JsonView.vue";
import draggable from "../../node_modules/vuedraggable/src/vuedraggable";
import { computed, ref } from "vue";
const props = defineProps(["parsedData"]);
const emit = defineEmits(["update:parsedData"]);

const formats = ["string", "array", "object", "number", "boolean"];
const flowData = computed(() => {
  return props.parsedData;
});
const toAddItem = ref(false);
const hideMyItem = ref({});

function delItem(parentDom, item, index) {
  flowData.value.splice(index, 1);

  if (hideMyItem.value[index]) hideMyItem.value[index] = false;

  onChange();
}

function addItem() {
  toAddItem.value = true;
}
function onChange() {
  emit("update:parsedData", flowData.value);
}

function cancelNewItem() {
  toAddItem.value = false;
}
function closeBlock(index, e) {
  hideMyItem.value[index] = hideMyItem.value[index] ? false : true;
}
function newItem(obj) {
  toAddItem.value = false;
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
  flowData.value.push(oj);
  onChange();
  cancelNewItem();
}
function onDragEnd() {
  emit("update:parsedData", flowData.value);
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
