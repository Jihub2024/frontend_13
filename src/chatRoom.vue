<template>
  <div class="chat-room">
    <div class="messages">
      <div v-for="(message, index) in messages" :key="index" class="message">
        <span v-if="message.isSent">{{ message.text }}</span>
        <span v-else class="received">{{ message.text }}</span>
      </div>
    </div>
    <div class="input-area">
      <input type="text" v-model="newMessage" @keyup.enter="sendMessage" placeholder="Type your message...">
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      messages: [],
      newMessage: ''
    };
  },
  methods: {
    sendMessage() {
      if (this.newMessage.trim() !== '') {
        this.messages.push({ text: this.newMessage, isSent: true });
        this.newMessage = '';
        // Simulate receiving a response after a short delay
        setTimeout(() => {
          this.messages.push({ text: 'Example response...', isSent: false });
        }, 500);
      }
    }
  }
};
</script>

<style scoped>
.chat-room {
  width: 400px;
  margin: auto;
}

.messages {
  border: 1px solid #ccc;
  height: 300px;
  overflow-y: scroll;
}

.message {
  padding: 10px;
}

.received {
  color: #333;
  font-style: italic;
}

.input-area {
  margin-top: 20px;
}

input[type="text"] {
  width: 300px;
  padding: 10px;
  border: 1px solid #ccc;
}

button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}
</style>
