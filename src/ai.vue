<template>
  <div class="ai-interface">
    <h1>AI Frontend Interface</h1>
    <div class="conversation">
      <div v-for="(message, index) in conversation" :key="index" class="message" :class="{ 'user-message': message.isUser }">
        <span v-if="!message.isUser" class="ai">AI:</span>
        <span>{{ message.text }}</span>
      </div>
    </div>
    <div class="input-area">
      <input type="text" v-model="userInput" @keyup.enter="sendMessage" placeholder="Type your message...">
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      conversation: [],
      userInput: ''
    };
  },
  methods: {
    sendMessage() {
      if (this.userInput.trim() !== '') {
        this.conversation.push({ text: this.userInput, isUser: true });
        // Send user input to AI for processing and get response
        // For demonstration, let's simulate AI response after a short delay
        setTimeout(() => {
          this.conversation.push({ text: 'Example AI response...', isUser: false });
        }, 500);
        this.userInput = '';
      }
    }
  }
};
</script>

<style scoped>
.ai-interface {
  width: 400px;
  margin: auto;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
}

.conversation {
  border: 1px solid #ccc;
  height: 300px;
  overflow-y: scroll;
}

.message {
  padding: 10px;
}

.user-message {
  text-align: right;
}

.ai {
  font-weight: bold;
  color: #007bff;
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
