<script setup>
import { ref, reactive, computed, nextTick } from 'vue'
import axios from "axios"

const apiKey = import.meta.env.VITE_API_KEY
const providers = { "languageDetection": "google", "sentimentAnalysis": "microsoft", "textGeneration": "openai" }
let entryMessage = reactive({ content: '', sentiment: null, score: null })
let messagesList = ref([])

const minimum_rate = 0.6
const isPositive = computed(() => {
  return entryMessage.sentiment === "Positive" && entryMessage.score > minimum_rate
})

const languageDetection = () => {

  const options = {
    method: 'POST',
    url: 'https://api.edenai.run/v2/translation/language_detection',
    headers: {
      authorization: 'Bearer ' + apiKey,
    },
    data: {
      providers: providers.languageDetection,
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
      authorization: 'Bearer ' + apiKey
    },
    data: {
      providers: providers.sentimentAnalysis,
      text: entryMessage.content,
      language: language
    }
  };

  return axios.request(options)
}

const textGeneration = () => {

  const options = {
    method: 'POST',
    url: 'https://api.edenai.run/v2/text/generation',
    headers: {
      authorization: 'Bearer ' + apiKey
    },
    data: {
      providers: providers.textGeneration,
      text: `Transform the following message with a negative sentiment into a positive message : ${entryMessage.content}`,
      temperature: 0.5,
      max_tokens: 250
    }
  };

  return axios.request(options)
}


const sendMessage = async () => {
  const languageResponse = await languageDetection()
  const language = languageResponse.data[providers.languageDetection].items[0].language
  const sentimentResponse = await sentimentAnalysis(language)
  entryMessage.score = sentimentResponse.data[providers.sentimentAnalysis].general_sentiment_rate
  entryMessage.sentiment = sentimentResponse.data[providers.sentimentAnalysis].general_sentiment
  if (isPositive.value) {
    messagesList.value.push(entryMessage.content)
    entryMessage.content = ''
  } else {
    return
  }

  const element = document.getElementById('messages-list')

  // scroll to bottom when new message is added
  await nextTick()
  element.scrollTop = element.scrollHeight
}

const transformMessage = async () => {
  const generationResponse = await textGeneration()
  entryMessage.content = generationResponse.data[providers.textGeneration].generated_text
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
    <div class="error-container" v-if="!isPositive && entryMessage.score">
      <p class="error-message">Your message is not kind enought ! </p>
      <button @click="transformMessage"> <svg xmlns="http://www.w3.org/2000/svg" width="30" height="30"
          viewBox="0 0 24 24">
          <path fill="currentColor"
            d="M12.01 4V1l-4 4l4 4V6c3.31 0 6 2.69 6 6c0 1.01-.25 1.97-.7 2.8l1.46 1.46A7.93 7.93 0 0 0 20.01 12c0-4.42-3.58-8-8-8zm0 14c-3.31 0-6-2.69-6-6c0-1.01.25-1.97.7-2.8L5.25 7.74A7.93 7.93 0 0 0 4.01 12c0 4.42 3.58 8 8 8v3l4-4l-4-4v3z" />
        </svg> Make my message nicer</button>

    </div>

  </div>
</template>

<style scoped></style>
