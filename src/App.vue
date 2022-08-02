<script setup>
import JsonEditor from "./index.js";
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

<template>
  <header>
    <div class="wrapper"></div>
  </header>

  <main>
    <div class="j-w">
      <h1 class="t">Vue3-Json-Edit</h1>
      <div class="editor-w clearfix flex justify-center">
        <div class="w-2">
          <div class="editor">
            <JsonEditor
              :options="{
                confirmText: 'confirm',
                cancelText: 'cancel',
              }"
              v-model:objData="jsonData"
            ></JsonEditor>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style>
body {
  font-size: 14px;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.clearfix {
  *zoom: 1;
}

.clearfix:before,
.clearfix:after {
  content: "";
  display: table;
}

.clearfix:after {
  clear: both;
}

.t {
  text-align: center;
  margin-top: 40px;
  margin-bottom: 60px;
}

.editor-w {
  margin: 0 auto;
  max-width: 1200px;
  padding: 0 20px;
}

.w-2 {
  float: left;
  width: 50%;
}
.flex {
  display: flex;
}
.justify-center {
  justify-content: center;
}

.editor {
  padding: 20px 60px;
}
</style>
