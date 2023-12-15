<template>
  <div>
    <input type="text" v-model="article">
    <button @click="run">询问</button>
    <div class="container" ref="container">
      <template v-for="item in list">
        <div class="left" v-if="item.type === 0" v-bind:key="item.value">
          {{ item.value }}
        </div>
        <div class="right" v-else v-bind:key="item">
          {{ item.value }}
        </div>
      </template>
    </div>

  </div>
</template>

<script setup>
import { GoogleGenerativeAI } from "@google/generative-ai";
import { ref } from "vue"

let article = ref("")
let text = ref("")
let list = ref([])
let container =ref()

// token 是自己申请的密钥
const genAI = new GoogleGenerativeAI("AIzaSyDyALuylqoid5wRx0dosbhXmLLAO08RSTM");

async function run() {
  const model = genAI.getGenerativeModel({ model: "gemini-pro" });

  const prompt = article.value
  list.value.push({
    value: prompt,
    type: 0
  })
  article.value = '';
  scrollToBottom(container.value)
  
  const result = await model.generateContent(prompt);
  const response = await result.response;
  
  text.value = response.text();
  await list.value.push({
    value: text.value,
    type: 1     
  })
  scrollToBottom(container.value)
}
function scrollToBottom(div) {
  div.scrollTop = div.scrollHeight;
}
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  height: 100px;
  overflow-y: scroll;
}

.left {
  align-self: flex-start;
  border: 1px solid red;
  width: 70vw;
}

.right {
  align-self: flex-end;
  border: 1px solid blue;
  width: 70vw;
}
</style>
