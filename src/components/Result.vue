<script setup>
const props = defineProps(['userAnswers']);
import { computed } from 'vue';
const emit = defineEmits(['restart']);
const corretAnswersCount = computed(() => {
    return props.userAnswers.filter(answer => answer.question.correct_answer == answer.answer).length;
})
const finalPoints = computed(() => {
    return Math.floor(100 * corretAnswersCount.value  / props.userAnswers.length); 
})
</script>
<template>
    <section class="result-screen container">
    <h1 v-if="corretAnswersCount == props.userAnswers.length">恭喜你全對答對🎉</h1>
    <h1>測驗結果：{{finalPoints}}分</h1>
    <h3 v-if="corretAnswersCount !== props.userAnswers.length">（你答對 {{ corretAnswersCount }} / {{ props.userAnswers.length }}）</h3>
    

    <ul>
        <li 
        v-for="(answer, index) in props.userAnswers" 
        :class="answer.question.correct_answer == answer.answer ? 'correct' : 'incorrect'"
        :key="index">
           
            <b>
                {{ answer.question.question }}
                <span v-html="answer.question.correct_answer == answer.answer ? '✅' : '❌'"></span>
            </b>
            <p>你的答案：{{ answer.answer }}</p>
            <p>正確答案：{{ answer.question.correct_answer }}</p>

        </li>
    </ul>

    <button @click="emit('restart')">挑戰新測驗</button>
</section>
</template>