<!-- The code has been modified -->
<template>
  <json-view
    @update:parsedData="onChange"
    v-model:parsedData="parsedData"
    @input="onChange"
  ></json-view>
</template>

<script setup>
import JsonView from "./JsonView.vue";
import { computed, ref, provide } from "vue";

const props = defineProps({
  objData: {
    type: [Object, Array],
    required: true,
  },
  options: {
    type: Object,
    default: function () {
      return {
        confirmText: "confirm",
        cancelText: "cancel",
      };
    },
  },
});
const emit = defineEmits(["update:objData"]);

provide("formBtnText", props.options);

const objData = computed(() => {
  return props.objData;
});

const parsedData = ref([]);
const wrapperType = ref("object");

parsedData.value = jsonParse(objData.value);

function onChange() {
  emit("update:objData", jsonUnParse(parsedData.value));
}

function jsonParse(jsonStr) {
  const parseJson = (json) => {
    let result = [];
    let keys = Object.keys(json);
    keys.forEach((k, index) => {
      let val = json[k];
      let parsedVal = val;
      if (getType(val) == "object") {
        parsedVal = parseJson(val);
      } else if (getType(val) == "array") {
        parsedVal = parseArray(val);
      }
      let opt = {
        name: k,
        type: getType(val),
      };
      if (opt.type == "array" || opt.type == "object") {
        opt.childParams = parsedVal;
        opt.remark = null;
      } else {
        opt.childParams = null;
        opt.remark = parsedVal;
      }
      result.push(opt);
    });
    return result;
  };

  const parseArray = (arrayObj) => {
    let result = [];
    for (let i = 0; i < arrayObj.length; ++i) {
      let val = arrayObj[i];
      let parsedVal = val;
      if (getType(val) == "object") {
        parsedVal = parseJson(val);
      } else if (getType(val) == "array") {
        parsedVal = parseArray(val);
      }
      let opt = {
        name: null,
        type: getType(val),
      };
      if (opt.type == "array" || opt.type == "object") {
        opt.childParams = parsedVal;
        opt.remark = null;
      } else {
        opt.childParams = null;
        opt.remark = parsedVal;
      }
      result.push(opt);
    }
    return result;
  };
  const parseBody = (data) => {
    let r = null;
    switch (getType(data)) {
      case "array":
        wrapperType.value = "array";
        r = parseArray(data);
        break;
      case "object":
        wrapperType.value = "object";
        r = parseJson(data);
        break;
    }
    return r;
  };
  return parseBody(jsonStr);
}
function getType(obj) {
  switch (Object.prototype.toString.call(obj)) {
    case "[object Array]":
      return "array";
      break;
    case "[object Object]":
      return "object";
      break;
    case "[object Null]":
    case "[object Function]":
    case "[object Undefined]":
      return "string";
      break;
    default:
      return typeof obj;
      break;
  }
}

function jsonUnParse(parsedData) {
  const object = (data) => {
    let arr = {};

    data.forEach((el, index) => {
      if (el.type !== "array" && el.type !== "object") {
        arr[el.name] = el.remark;
      } else if (el.type == "object") {
        arr[el.name] = object(el.childParams);
      } else {
        arr[el.name] = array(el.childParams);
      }
    });
    return arr;
  };

  const array = (data) => {
    let arr = [];

    data.forEach((el, index) => {
      if (el.type !== "array" && el.type !== "object") {
        arr.push(el.remark);
      } else if (el.type == "object") {
        arr.push(object(el.childParams));
      } else {
        arr.push(array(el.childParams));
      }
    });

    return arr;
  };

  return object(parsedData);
}
</script>
