<template>
  <div class="">
    <span class="chat-title">Chatira</span>
    <div class="chat-wrapper">
      <Chat />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Chat from "@/components/Chat";

export default {
  name: "Home",
  components: {
    Chat
  }
};
</script>

<style>
.chat-title {
  display: block;
  font-size: 35px;
  margin-top: 10px;
}

.chat-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
