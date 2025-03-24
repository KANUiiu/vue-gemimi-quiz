<script setup>
import { ref, computed } from 'vue';
const props = defineProps(['questions']);

const emit = defineEmits(['store-answers', 'end-quiz']);

const currentQuestion = ref(0);
const selectedOption = ref(null);

const arrangeOptions = computed(() => {
    let options = [...props.questions[currentQuestion.value].incorrect_answers]
    const randomNum = Math.floor((Math.random() * options.length));
    options.splice(randomNum, 0, props.questions[currentQuestion.value].correct_answer);
    return options;
})

const submitAnswer = () => {
    emit('store-answers', {
        question: props.questions[currentQuestion.value],
        answer: selectedOption.value,
    })
    selectedOption.value = null;
    if(currentQuestion.value === props.questions.length - 1){
        currentQuestion.value = 0;
        emit('end-quiz')
    }else{
        currentQuestion.value++;
    }  
}



// const props = defineProps({
//     questions: {
//         type: Array,
//         required: true
//     }
// });

// onMounted(() => {
//     console.log(props.questions);
// })
</script> 

<template>
    <section class="quiz container">
        <div class="header">
            <h2>測驗題目</h2>
            <p>第 {{ currentQuestion + 1 }} 題／共 {{ props.questions.length }} 題</p>
        </div>
        <progress max="100" :value="(currentQuestion)/props.questions.length*100"></progress>
        <div class="question">
            <h3>{{ props.questions[currentQuestion].question }}</h3>
        </div>  

        <div class="answers">
            <button 
                class="answer"  
                :class="{active: answer === selectedOption}"
                v-for="answer in arrangeOptions"
                key="answer"
                @click="selectedOption = answer"
            >
                {{ answer }}
            </button>
        </div>

        <button 
        v-if="selectedOption" 
        @click="submitAnswer"
        >送出</button>

    </section>
</template>
