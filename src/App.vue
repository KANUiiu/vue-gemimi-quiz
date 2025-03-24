<script setup>
  import { GoogleGenerativeAI, SchemaType } from "@google/generative-ai";
  import StartScreen from './components/StartScreen.vue';
  import Quiz from './components/Quiz.vue';
  import Loader from './components/Loader.vue';
  import Result from "./components/Result.vue";
  import {ref} from 'vue';
  import { config } from "dotenv";

  const questions = ref('');
  const status = ref('start');  
  const errorMsg = ref('');
  const userAnswers = ref([]);

  const restartApp = () => {
    questions.value = '';
    status.value = 'start';
    errorMsg.value = '';
    userAnswers.value = [];
  }
  
  const storeAnswers = (answer) => {
    userAnswers.value.push(answer);
  }

  const startQuiz = async (topic) => {
  status.value = 'loading';
  const apiKey = process.env.VUE_APP_API_KEY;
  const genAI = new GoogleGenerativeAI(apiKey);

//   const schema = {
//   description: "List of quiz questions",
//   type: SchemaType.ARRAY,
//   items: {
//     type: SchemaType.OBJECT,
//     properties: {
//       response_code: {
//         type: SchemaType.NUMBER,
//         description: "The response code for the quiz API response",
//         nullable: false,
//       },
//       results: {
//         type: SchemaType.ARRAY,
//         description: "List of quiz questions and their details",
//         items: {
//           type: SchemaType.OBJECT,
//           properties: {
//             type: {
//               type: SchemaType.STRING,
//               description: "Type of the question (e.g., multiple, boolean)",
//               nullable: false,
//             },
//             difficulty: {
//               type: SchemaType.STRING,
//               description: "Difficulty level of the question",
//               nullable: false,
//             },
//             category: {
//               type: SchemaType.STRING,
//               description: "Category of the question",
//               nullable: false,
//             },
//             question: {
//               type: SchemaType.STRING,
//               description: "The question text",
//               nullable: false,
//             },
//             correct_answer: {
//               type: SchemaType.STRING,
//               description: "The correct answer to the question",
//               nullable: false,
//             },
//             incorrect_answers: {
//               type: SchemaType.ARRAY,
//               description: "List of incorrect answers",
//               items: {
//                 type: SchemaType.STRING,
//                 description: "An incorrect answer option",
//               },
//               nullable: false,
//             },
//           },
//           required: [
//             "type",
//             "difficulty",
//             "category",
//             "question",
//             "correct_answer",
//             "incorrect_answers",
//           ],
//         },
//         nullable: false,
//       },
//     },
//     required: ["response_code", "results"],
//   },
// };

const schema = {
  description: "List of quiz questions",
  type: SchemaType.OBJECT,
  properties: {
    response_code: {
      type: SchemaType.NUMBER,
      description: "Response code indicating the status of the request",
      nullable: false,
    },
    results: {
      type: SchemaType.ARRAY,
      description: "Array of quiz questions",
      items: {
        type: SchemaType.OBJECT,
        properties: {
          type: {
            type: SchemaType.STRING,
            description: "Type of question (e.g., multiple choice, boolean)",
            nullable: false,
          },
          difficulty: {
            type: SchemaType.STRING,
            description: "Difficulty level of the question (easy, medium, hard)",
            nullable: false,
          },
          category: {
            type: SchemaType.STRING,
            description: "Category of the question",
            nullable: false,
          },
          question: {
            type: SchemaType.STRING,
            description: "The quiz question",
            nullable: false,
          },
          correct_answer: {
            type: SchemaType.STRING,
            description: "The correct answer to the question",
            nullable: false,
          },
          incorrect_answers: {
            type: SchemaType.ARRAY,
            description: "Array of incorrect answers",
            items: {
              type: SchemaType.STRING,
            },
            nullable: false,
          },
        },
        required: [
          "type",
          "difficulty",
          "category",
          "question",
          "correct_answer",
          "incorrect_answers",
        ],
      },
      nullable: false,
    },
  },
  required: ["response_code", "results"],
};

const model = genAI.getGenerativeModel({
  model: "gemini-1.5-pro",
  generationConfig: {
    responseMimeType: "application/json",
    responseSchema: schema,
  },
});

//Rendering Errors to UI
try {
    const result = await model.generateContent(
    `Create 5 quiz questions about ${topic}.
    Always create the quiz questions in Traditional Chinese.
    `,
   );
   questions.value = JSON.parse(result.response.text());
   status.value = 'quiz';
} catch(error){
  errorMsg.value = error;
  status.value = 'start';
};


}

</script>

<template>
  <div id="app">
    <header>
      <div class="container">
        <h1>Gemini 來考考你</h1>
        <img src="./assets/logo.png" alt="logo" class="logo" />
      </div>
    </header>
    <StartScreen v-if="status === 'start'" :errorMsg="errorMsg" @start-quiz="startQuiz" />
    <Loader v-if="status === 'loading'" />
    <Quiz v-if="status === 'quiz'" @end-quiz="status = 'end'" @store-answers="storeAnswers" :questions="questions.results" /> 
    <Result v-if="status === 'end'" :userAnswers="userAnswers" @restart="restartApp" />
 </div>
</template>

<style scoped>

</style>
