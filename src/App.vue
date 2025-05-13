<template>
  <div id="app">
    <Chat />
  </div>
</template>

<script>
import Chat from './components/Chat.vue';
import { Picker } from 'emoji-mart-vue-fast';
import 'emoji-mart-vue-fast/css/emoji-mart.css';

export default {
  components: {
    Chat,
    Picker,
  },
};
</script>
