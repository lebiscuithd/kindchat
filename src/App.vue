<script setup>
import { ref, nextTick } from 'vue'

let entryMessage = ref({ content: '', score: null })
let messagesList = ref([])


const sendMessage = async () => {
  messagesList.value.push(entryMessage.value.content)
  const element = document.getElementById('messages-list')
  entryMessage.value.content = ''
  await nextTick()
  element.scrollTop = element.scrollHeight
}

</script>

<template>
  <div>
    <h1>😀 KindChat</h1>
    <span>Powered by Eden AI</span>
    <div id="messages-list">
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
