<script setup>
    import { ref } from 'vue'
    const topic = ref('');
    const emit = defineEmits(['start-quiz']);
    const props = defineProps(['errorMsg']);
</script>

<template> 
    <section class="start-screen container">
        <h2>決定一個主題讓 Gemini 來出題吧</h2>
        <input v-model="topic" type="text" placeholder="請輸入主題...">
        <button @click="emit('start-quiz', topic)" :disabled="!topic">開始測驗</button>
        <p class="error" v-if="errorMsg">網站出現錯誤，請稍後再試。</p>
  </section>
</template>