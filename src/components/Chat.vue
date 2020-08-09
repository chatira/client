<template>
  <div class="chat">
    <div class="chat-header"></div>
    <div class="chat-content">
      <div
        v-for="(chat, index) in chats"
        :key="index"
        :class="
          `chat-content__item ${
            chat.user.id === userID
              ? 'chat-content__item--sender'
              : 'chat-content__item--receiver'
          }`
        "
      >
        <span>{{ chat.text }}</span>
      </div>
    </div>
    <div class="chat-footer">
      <img
        class="chat-footer__send-icon"
        src="@/assets/send.svg"
        @click="sendMessage"
      />
      <textarea
        v-model="message"
        class="chat-footer__message-box"
        placeholder="type your message..."
        @keydown.enter="sendMessage"
      ></textarea>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";

const socket = io("http://localhost:3000");
const userID = `User-${Math.floor(Math.random() * 1000000)}`;

export default {
  name: "Chat",
  data() {
    return {
      chats: [],
      userID: userID,
      message: "",
    };
  },
  mounted() {
    let user = {
      id: this.userID
    }

    socket.on("connect", function() {
      console.log("connect");
      socket.emit("new-user", user);
    });

    socket.on("message", data => {
      this.updateMessage(data);
      console.log("server message: ", data);
    });

    socket.on("disconnect", function() {
      console.log("disconnect");
    });
  },
  methods: {
    sendEvent(data) { 
      socket.emit("message", data);
    },
    sendMessage() {
      if (!this.message) return;

      var messageItem = {
        id: 1,
        text: this.message,
        user: {
          id: this.userID
        }
      };

      this.chats.push(messageItem);
      this.sendEvent(messageItem);
      this.message = "";
    },
    updateMessage(data) {
      if (data.user.id !== this.userID) {
        this.chats.push(data);
      }
    }
  },
};
</script>

<style scoped>
.chat {
  width: 400px;
  height: 500px;
  display: flex;
  margin-top: 50px;
  flex-direction: column;
  background-color: white;
  justify-content: space-between;
  box-shadow: 0 5px 35px rgba(0, 0, 0, 0.2);
}

@media screen and (max-width: 768px) {
  .chat {
    width: 350px;
  }
}

.chat-header {
  border-top: 5px solid #15c6ae;
}

.chat-content {
  flex: 1;
  display: flex;
  padding: 16px;
  overflow: auto;
  max-height: 100%;
  flex-direction: column;
}

.chat-content__item {
  display: block;
  min-width: 50px;
  max-width: 200px;
  color: #535353;
  padding: 7px 10px;
  margin-bottom: 5px;
}

.chat-content__item--receiver {
  margin-right: auto;
  background-color: white;
  border: 1px solid #ebebeb;
  border-radius: 0 5px 5px 0;
}

.chat-content__item--sender {
  margin-left: auto;
  background-color: #ebebeb;
  border-radius: 5px 0 0 5px;
}

.chat-footer {
  padding: 20px;
  max-height: 20px;
  position: relative;
  background-color: #ebebeb;
}

.chat-footer__send-icon {
  top: 50%;
  right: 20px;
  cursor: pointer;
  max-width: 20px;
  position: absolute;
  transition: all 0.2s ease;
  transform: translateY(-50%);
}

.chat-footer__send-icon:active {
  max-width: 25px;
}

.chat-footer__message-box {
  margin: 0;
  border: 0;
  padding: 0;
  width: 100%;
  resize: none;
  height: 100%;
  outline: none;
  font-size: 18px;
  color: #535353;
  overflow: hidden;
  font-family: sans-serif;
  background-color: #ebebeb;
}

.chat-footer__message-box::blur {
  border: 0;
  outline: none;
}
</style>
