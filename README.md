# Vue3-Json-Editor
>**(fork vue-json-edit - credits original: [https://github.com/jinkin1995/vue-json-edit](#https://github.com/jinkin1995/vue-json-edit))**
>
>
>**Visual JSON Editor built as an vue component. Provides a basic GUI**

![alt text](https://github.com/AnonX98/vue3-json-edit/blob/master/image.jpg?raw=true)

## Demo

[Link](https://AnonX98.github.io/vue3-json-edit/)

## Getting Started

```sh
npm install vue-json-edit --save
```

## Usage

To use it globally
```sh
import Vue from 'vue'
import JsonEditor from 'vue-json-edit'

Vue.use(JsonEditor)
```
Otherwise just import
```sh
import JsonEditor from 'vue-json-edit'
```

## Example
Single file component


```sh
<template>
<JsonEditor
    :options="{
        confirmText: 'confirm',
        cancelText: 'cancel',
    }"
	v-model:objData="jsonData"
	/>
</template>
<script setup>
import JsonEditor from "./JsonEditor.vue";
import { ref } from "vue";

const jsonData = ref({
  name: "may",
  age: null,
  address: [
    "Panyu Shiqiao on Canton",
    "Tianhe",
    {
      city: "forida meta 11",
    },
  ],
  others: {
    id: 1246,
    joinTime: "2017-08-20. 10:20",
    description: "another",
  },
});
</script>
```
## Props

* objData: json data
* options
  * confirmText: strings of the confirm button
  *   cancelText: strings of the cancel button
