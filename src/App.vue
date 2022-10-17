<script setup>
import { ref, nextTick } from 'vue'

let entryMessage = ref({ content: '', score: null })
let messagesList = ref([])


const sendMessage = async () => {
  messagesList.value.push(entryMessage.value.content)
  const element = document.getElementById('messages-list')
  entryMessage.value.content = ''
  await nextTick()
  // scroll to bottom when new message is added
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

  </div>
</template>

<style scoped>

</style>
