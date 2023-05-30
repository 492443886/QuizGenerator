<script setup>
import { ref } from "vue";
import Question from './Question.vue'
import Loading from './Loading.vue'

const message = ref('')
const questions = ref()
const query = ref("")
const isM = ref(false)
const isLoadig = ref(false)

const askQuiz = async () => {

    isLoadig.value = true;

    console.log(query.value)

    const quest = `Create 2 questions about ${query.value}. The format of the quiz must be Json and should be like this:

    1. { "question": "What is a Corporate Signing Key?", "a": "A public key used by a company to sign legal documents", "b": "A private key used by a company to sign legal documents", "c": "A key used by a company to encrypt sensitive personnel information", "d": "A key used by a company to encrypt press releases", "answer": "b" } 
    2. { "question": "Why is it important to split a private key among multiple people?", "a": "To make it easier to reconstitute the key", "b": "To make it harder for any single individual to act fully on behalf of the company", "c": "To make it easier to physically transport the key", "d": "To make it harder to use the key on a secure network connection", "answer": "b" }'

`
    console.log(quest)
    const response = await fetch('https://www.chatbase.co/api/v1/chat', {
        method: 'POST',
        headers: {
            Authorization: "Bearer db92407f-cfe9-440a-a13d-34370b6011e6"
        },
        body: JSON.stringify({
            messages: [
                { content: quest, role: 'user' }
            ],
            chatId: 'FxziuGST1hYkT2ARNdR1p',
            stream: true,
            temperature: 0.5,
            output_format: "json"
        })
    });

    console.log(response.body)
    const data = response.body;

    if (!data) {
        // error happened
    }

    const reader = data.getReader();
    const decoder = new TextDecoder();
    let done = false;


    //let alltext = ''
    while (!done) {
        const { value, done: doneReading } = await reader.read();
        done = doneReading;
        const chunkValue = decoder.decode(value);
        console.log(chunkValue); // This will log chunks of the chatbot reply until the reply is finished.
        message.value += chunkValue

    }

    const inputString = message.value
    //const inputString = 'Sure, I can create a quiz about private keys. Here are two questions for you: 1. { "question": "What is a Corporate Signing Key?", "a": "A public key used by a company to sign legal documents", "b": "A private key used by a company to sign legal documents", "c": "A key used by a company to encrypt sensitive personnel information", "d": "A key used by a company to encrypt press releases", "answer": "b" } 2. { "question": "Why is it important to split a private key among multiple people?", "a": "To make it easier to reconstitute the key", "b": "To make it harder for any single individual to act fully on behalf of the company", "c": "To make it easier to physically transport the key", "d": "To make it harder to use the key on a secure network connection", "answer": "b" }';

    const regex = /{([^}]+)}/g;
    const matches = inputString.match(regex);

    // Convert each match to JSON object and store in an array
    const jsonArray = [];
    matches.forEach(match => {
        const jsonObject = JSON.parse(match);
        jsonArray.unshift(jsonObject);
    });

    console.log(jsonArray)
    questions.value = []
    questions.value = jsonArray
    isLoadig.value = false;
}


</script>

<template>
    <h1 class="title">AI Quiz Generator</h1>

    <label class="label">What is your quiz about?</label>

    <input type="text" v-model="query" />
    <Loading v-if="isLoadig"></Loading>
    <button v-if="!isLoadig" @click="askQuiz" :disabled="query === ''">Generate</button>
    <br />

    <button v-show="isM === false" @click="isM = true" class="btn">Show Message</button>
    <button v-show="isM === true" @click="isM = false" class="btn">Hide Message</button>

    <div v-show="isM === true" class="message">{{ message }}</div>

    <div v-for="q in questions" class="question">
        <Question :question="q.question" :optionA="q.a" :optionB="q.b" :optionC="q.c" :optionD="q.d" :answer="q.answer">
        </Question>
    </div>
</template>
  
<style scoped>
.title {
    text-align: center;
    color: #333;
}

.label {
    display: block;
    margin-bottom: 10px;

}

input[type="text"] {
    width: 100%;
    padding: 5px 2px;
    border: 1px solid #ccc;
    border-radius: 3px;
}

button {
    margin-top: 10px;
    padding: 5px 10px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 3px;
    cursor: pointer;
}

.btn {
    margin-right: 5px;
}

.message {
    margin-top: 10px;
    padding: 10px;
    background-color: #f5f5f5;
    border: 1px solid #ccc;
    border-radius: 3px;
}

.question {
    margin-top: 20px;
    padding: 10px;
    background-color: #f9f9f9;
    border: 1px solid #ccc;
    border-radius: 3px;
}
</style>
  