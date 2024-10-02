<template>
    <div class="typing-tutor">
      <h1>Клавиатурный тренажер</h1>
      <div class="text-display">
        <span
          v-for="(char, index) in textArray"
          :key="index"
          :class="getCharClass(index)"
        >
          {{ char }}
        </span>
      </div>
      <input
        ref="inputField"
        v-model="userInput"
        @input="handleInput"
        @keydown="handleKeyDown"
        type="text"
        class="hidden-input"
        autocomplete="off"
        autofocus
      />
      <div class="stats">
        <p>Скорость: {{ wpm }} WPM</p>
        <p>Точность: {{ accuracy }}%</p>
        <p>Время: {{ timeLeft }} сек</p>
      </div>
      <Keyboard :currentKey="currentKey" />
      <button @click="resetGame">Сброс</button>
    </div>
  </template>
  
  <script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';
  import Keyboard from './Keyboard.vue';
  
  export default defineComponent({
    name: 'TypingTutor',
    components: {
      Keyboard,
    },
    setup() {
      const text = ref('');
      const textArray = ref<string[]>([]);
      const userInput = ref('');
      const currentCharIndex = ref(0);
      const correctChars = ref(0);
      const totalChars = ref(0);
      const wpm = ref(0);
      const accuracy = ref(100);
      const timeLeft = ref(60);
      const timer = ref<number | null>(null);
    const currentKey = ref('');
  
      const fetchText = async () => {
        try {
          const response = await axios.get(
            'https://baconipsum.com/api/?type=all-meat&paras=2&start-with-lorem=1'
          );
          text.value = response.data.join(' ');
          textArray.value = text.value.split('');
        } catch (error) {
          console.error('Error fetching text:', error);
        }
      };
  
      const startTimer = () => {
        timer.value = setInterval(() => {
          if (timeLeft.value > 0) {
            timeLeft.value--;
          } else {
            if (timer.value !== null) {
                clearInterval(timer.value);
                timer.value = null;
        }           
         timer.value = null;
          }
        }, 1000);
      };
  
      const handleInput = () => {
        const currentChar = textArray.value[currentCharIndex.value];
        const inputChar = userInput.value[userInput.value.length - 1];
  
        if (inputChar === currentChar) {
          correctChars.value++;
          currentCharIndex.value++;
        } else {
          // Не позволяем двигаться дальше
          userInput.value = userInput.value.slice(0, -1);
        }
  
        totalChars.value++;
  
        // Обновляем статистику
        accuracy.value = Math.round(
          (correctChars.value / totalChars.value) * 100
        );
        wpm.value = Math.round((correctChars.value / 5) / ((60 - timeLeft.value) / 60));
      };
  
      const handleKeyDown = (event: KeyboardEvent) => {
        currentKey.value = event.key;
      };
  
      const getCharClass = (index: number) => {
        if (index < currentCharIndex.value) {
          return 'correct';
        } else if (index === currentCharIndex.value) {
          return 'current';
        } else {
          return '';
        }
      };
  
      const resetGame = () => {
        userInput.value = '';
        currentCharIndex.value = 0;
        correctChars.value = 0;
        totalChars.value = 0;
        wpm.value = 0;
        accuracy.value = 100;
        timeLeft.value = 60;
        currentKey.value = '';
        if (timer.value) {
          clearInterval(timer.value);
        }
        fetchText();
        startTimer();
      };
  
      onMounted(() => {
        fetchText();
        startTimer();
      });
  
      return {
        textArray,
        userInput,
        wpm,
        accuracy,
        timeLeft,
        handleInput,
        getCharClass,
        resetGame,
        handleKeyDown,
        currentKey,
      };
    },
  });
  </script>
  
  <style scoped>
  .typing-tutor {
    text-align: center;
  }
  
  .text-display {
    font-size: 24px;
    margin: 20px;
  }
  
  .text-display .correct {
    color: green;
  }
  
  .text-display .current {
    text-decoration: underline;
  }
  
  .hidden-input {
    opacity: 0;
    position: absolute;
  }
  
  .stats {
    margin: 20px;
  }
  </style>
  