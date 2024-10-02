<template>
    <div class="keyboard">
      <div class="key-row" v-for="(row, rowIndex) in keys" :key="rowIndex">
        <div
          class="key"
          v-for="key in row"
          :key="key"
          :class="{ pressed: key === currentKey }"
        >
          {{ key }}
        </div>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, PropType } from 'vue';
  
  export default defineComponent({
    name: 'Keyboard',
    props: {
      currentKey: {
        type: String as PropType<string>,
        required: true,
      },
    },
    data() {
      return {
        keys: [
          ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'],
          ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L'],
          ['Z', 'X', 'C', 'V', 'B', 'N', 'M'],
        ],
      };
    },
  });
  </script>
  
  <style scoped>
  .keyboard {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .key-row {
    display: flex;
  }
  
  .key {
    width: 30px;
    height: 30px;
    margin: 2px;
    border: 1px solid #000;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .key.pressed {
    background-color: yellow;
  }
  </style>
  