<template>
  <div class="chatbot-container">
    <div class="chat-button" @click="toggleChat">
      <img src="@/assets/images/chat-icon.svg" alt="客服">
    </div>
    
    <div class="chat-window" v-if="isOpen">
      <div class="chat-header">
        <h3>AI客服助手</h3>
        <button @click="toggleChat">×</button>
      </div>
      
      <div class="chat-messages">
        <div v-for="(msg, index) in messages" :key="index" 
             :class="['message', msg.sender]">
          {{ msg.text }}
        </div>
      </div>
      
      <div class="chat-input">
        <input v-model="inputMessage" @keyup.enter="sendMessage" 
               placeholder="输入您的问题...">
        <button @click="sendMessage">发送</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ChatBot',
  data() {
    return {
      isOpen: false,
      inputMessage: '',
      messages: [
        { text: '您好！我是AI客服，请问有什么可以帮您？', sender: 'bot' }
      ]
    }
  },
  methods: {
    toggleChat() {
      this.isOpen = !this.isOpen
    },
    async sendMessage() {
      if (!this.inputMessage.trim()) return
      
      // 添加用户消息
      this.messages.push({
        text: this.inputMessage,
        sender: 'user'
      })
      
      const userMessage = this.inputMessage
      this.inputMessage = ''
      
      // 模拟AI回复 - 实际使用时替换为API调用
      setTimeout(() => {
        this.messages.push({
          text: this.getBotResponse(userMessage),
          sender: 'bot'
        })
      }, 500)
    },
    getBotResponse(message) {
      // 简单回复逻辑 - 实际使用时替换为API调用
      if (message.includes('预约') || message.includes('挂号')) {
        return '您可以通过页面上的科室选择进行在线预约挂号。'
      } else if (message.includes('时间') || message.includes('营业')) {
        return '我院营业时间为周一至周日 8:00-17:30。'
      } else {
        return '我已收到您的咨询，关于医院预约和就诊的问题都可以问我。'
      }
    }
  }
}
</script>

<style scoped>
.chatbot-container {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 1000;
}

.chat-button {
  width: 60px;
  height: 60px;
  background: #d7292b;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0,0,0,0.2);
}

.chat-button img {
  width: 30px;
  height: 30px;
}

.chat-window {
  width: 300px;
  height: 400px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 2px 15px rgba(0,0,0,0.2);
  display: flex;
  flex-direction: column;
}

.chat-header {
  background: #d7292b;
  color: white;
  padding: 10px 15px;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.chat-header button {
  background: none;
  border: none;
  color: white;
  font-size: 20px;
  cursor: pointer;
}

.chat-messages {
  flex: 1;
  padding: 10px;
  overflow-y: auto;
}

.message {
  margin: 5px 0;
  padding: 8px 12px;
  border-radius: 15px;
  max-width: 80%;
}

.message.user {
  background: #e3f2fd;
  margin-left: auto;
  border-bottom-right-radius: 5px;
}

.message.bot {
  background: #f1f1f1;
  margin-right: auto;
  border-bottom-left-radius: 5px;
}

.chat-input {
  display: flex;
  padding: 10px;
  border-top: 1px solid #eee;
}

.chat-input input {
  flex: 1;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 20px;
  margin-right: 5px;
}

.chat-input button {
  background: #d7292b;
  color: white;
  border: none;
  border-radius: 20px;
  padding: 8px 15px;
  cursor: pointer;
}
</style>