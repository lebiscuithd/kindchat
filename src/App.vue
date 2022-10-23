<script setup>
import { ref, reactive, computed, nextTick } from 'vue'
import axios from "axios"

let entryMessage = reactive({ content: '', score: null })
let messagesList = ref([])

const minimum_rate = 0.6
const isPositive = computed(() => {
  return entryMessage.score > minimum_rate
})

const languageDetection = () => {
  const options = {
    method: 'POST',
    url: 'https://api.edenai.run/v2/translation/language_detection',
    headers: {
      authorization: 'Bearer ' + import.meta.env.VITE_API_KEY
    },
    data: {
      providers: 'google',
      text: entryMessage.content
    }
  };
  return axios.request(options)
}

const sentimentAnalysis = (language) => {
  const options = {
    method: 'POST',
    url: 'https://api.edenai.run/v2/text/sentiment_analysis',
    headers: {
      authorization: 'Bearer ' + import.meta.env.VITE_API_KEY
    },
    data: {
      providers: 'microsoft',
      text: entryMessage.content,
      language: language
    }
  };

  return axios.request(options)
}

const sendMessage = async () => {
  const languageResponse = await languageDetection()
  const language = (languageResponse.data.google.items[0].language)
  const sentimentResponse = await sentimentAnalysis(language)
  entryMessage.score = sentimentResponse.data.microsoft.items.find(x => x.sentiment === "positive").sentiment_rate
  if (isPositive.value) {
    messagesList.value.push(entryMessage.content)
    entryMessage.content = ''
  } else {
    return
  }
  await nextTick()
  // scroll to bottom when new message is added
  const element = document.getElementById('messages-list')
  element.scrollTop = element.scrollHeight
}

</script>

<template>
  <div>

    <h1>😊 KindChat</h1>
    <div id="powered-by">
      <span>Powered by </span>
      <img src="./assets/edenai_logo.png" id="logo-edenai" width="70" alt="">
    </div>

    <div id="messages-list">
      <div class="default-message">
        👋 Welcome to KindChat ! Only nice messages are allowed here.
      </div>
      <div class="default-message">
        Thanks to <a target="_blank" href="https://edenai.co/">
          Eden AI
        </a>NLP APIs your message will be analyzed and scored before being sent.
      </div>
      <div class="validated-message" v-for="message in messagesList" :key="message">
        {{ message }}
      </div>
    </div>
    <form id="input-area" @submit.prevent="sendMessage()">
      <input v-model="entryMessage.content" id="input-message" type="text" placeholder="Type your kind message here"
        minlength="1">
      <button type="submit">Send message</button>
    </form>
    <p class="error-message" v-if="!isPositive && entryMessage.score">😣 Your message is not kind enought ! Score :
      {{entryMessage.score}}</p>
  </div>
</template>

<style scoped>

</style>
