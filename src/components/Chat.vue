<template>
    <div class="chat-container">
      <h1 class="title">🤖 AI Chatbot</h1>

      <div class="chat-box">
        <div
          v-for="(msg, index) in messages"
          :key="index"
          :class="['message', msg.sender.toLowerCase()]"
        >
          <img
            :src="msg.sender === 'User' ? userAvatar : botAvatar"
            class="avatar"
          />
          <div class="bubble">
            <strong>{{ msg.sender }}:</strong>
            <div>{{ msg.text }}</div>
            <div class="timestamp">{{ msg.time }}</div>
          </div>
        </div>
        <div v-if="loading" class="typing">🤖 Bot is typing...</div>
      </div>

      <div class="input-box">
        <input
          v-model="userInput"
          @keyup.enter="sendMessage"
          placeholder="Type your message..."
          class="chat-input"
        />
        <button @click="toggleEmojiPicker" class="emoji-btn">😊</button>
      </div>


      <div v-if="showEmojiPicker" class="emoji-picker-container">
        <Picker @select="addEmoji" />
      </div>
    </div>
  </template>

  <script>
  import { Picker } from 'emoji-mart-vue';
  import 'emoji-mart-vue/css/emoji-mart.css';
  import axios from "axios";

  export default {
    name: "Chat",
    components: {
      Picker
    },
    data() {
      return {
        userInput: "",
        messages: [],
        loading: false,
        userAvatar: 'https://i.pravatar.cc/40?img=3',
        botAvatar: 'https://cdn-icons-png.flaticon.com/512/4712/4712037.png',
        showEmojiPicker: false,
      };
    },
    methods: {
      toggleEmojiPicker() {
        this.showEmojiPicker = !this.showEmojiPicker;
      },
      addEmoji(emoji) {
        this.userInput += emoji.native;
        this.showEmojiPicker = false;
      },
      async sendMessage() {
        if (!this.userInput.trim()) return;
        const timeNow = new Date().toLocaleTimeString();

        this.messages.push({
          sender: "User",
          text: this.userInput,
          time: timeNow,
        });

        const messageToSend = this.userInput;
        this.userInput = "";
        this.loading = true;

        try {
          const response = await axios.post("http://127.0.0.1:8000/api/chat/", {
            message: messageToSend,
          });

          this.messages.push({
            sender: "Bot",
            text: response.data.response,
            time: new Date().toLocaleTimeString(),
          });
        } catch (error) {
          this.messages.push({
            sender: "Bot",
            text: "⚠️ Error: Unable to reach the server.",
            time: new Date().toLocaleTimeString(),
          });
        } finally {
          this.loading = false;
        }
      },
    },
  };
  </script>

  <style scoped>
  .chat-container {
    max-width: 600px;
    margin: auto;
    padding: 15px;
    font-family: Arial, sans-serif;
  }
  .chat-box {
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 10px;
    min-height: 300px;
    max-height: 400px;
    overflow-y: auto;
    margin-bottom: 10px;
    background-color: #f9f9f9;
  }
  .input-box {
    display: flex;
    align-items: center;
    margin-top: 1rem;
  }
  .emoji-btn {
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    margin-left: 8px;
  }
  .input-box input {
    flex: 1;
    padding: 10px;
    font-size: 1em;
    border-radius: 4px;
    border: 1px solid #ccc;
  }
  .message.User {
    text-align: right;
    color: blue;
    margin-bottom: 8px;
  }
  .message.Bot {
    text-align: left;
    color: green;
    margin-bottom: 8px;
  }
  .typing {
    font-style: italic;
    color: #888;
    margin-top: 5px;
  }
  .message {
    display: flex;
    align-items: flex-start;
    margin-bottom: 12px;
  }
  .message.user {
    flex-direction: row-reverse;
    text-align: right;
  }
  .avatar {
    width: 35px;
    height: 35px;
    border-radius: 50%;
    margin: 0 10px;
  }
  .bubble {
    background-color: #f0f0f0;
    padding: 10px 15px;
    border-radius: 15px;
    max-width: 70%;
    position: relative;
  }
  .timestamp {
    font-size: 0.75rem;
    color: #888;
    margin-top: 4px;
  }
  .emoji-picker-container {
    position: absolute;
    bottom: 80px;
    right: 20px;
    z-index: 999;
  }
  </style>
