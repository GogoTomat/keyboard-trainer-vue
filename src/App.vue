<template>
  <div id="app">
    <TypingTutor />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import TypingTutor from './components/TypingTutor.vue';

export default defineComponent({
  name: 'App',
  components: {
    TypingTutor,
  },
});
</script>
