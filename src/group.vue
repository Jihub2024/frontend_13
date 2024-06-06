<template>
  <div class="chat-room">
    <div class="messages">
      <div v-for="(message, index) in messages" :key="index" class="message">
        <span class="sender">{{ message.sender }}:</span>
        <span class="content">{{ message.content }}</span>
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
      messages: [
        { sender: 'John', content: 'Hello everyone!' },
        { sender: 'Alice', content: 'Hi John!' },
        { sender: 'Bob', content: 'How are you all doing?' }
      ],
      newMessage: ''
    };
  },
  methods: {
    sendMessage() {
      if (this.newMessage.trim() !== '') {
        this.messages.push({ sender: 'You', content: this.newMessage });
        this.newMessage = '';
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

.sender {
  font-weight: bold;
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
