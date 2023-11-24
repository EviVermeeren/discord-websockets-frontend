<script setup>
import { ref, onMounted } from "vue";

let message = ref("Hello World");
let messages = ref([]);

let socket = null;

onMounted(() => {
  socket = new WebSocket(
    "wss://discord-websockets-backend.onrender.com/primus"
  );

  socket.onmessage = (event) => {
    let newMessage = JSON.parse(event.data);

    if (newMessage.action === "newMessage") {
      messages.value.push(newMessage.message);
    }
  };
});

const sendMessage = () => {
  let newMessage = {
    message: message.value,
    action: "newMessage",
  };

  socket.send(JSON.stringify(newMessage));

  message.value = "";
};
</script>

<template>
  <div>
    <h1>Chat</h1>
    <ul>
      <li v-for="message in messages" :key="message">{{ message }}</li>
    </ul>
    <div>
      <input v-model="message" type="text" />
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<style scoped></style>
