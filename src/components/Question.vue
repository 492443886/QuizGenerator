<template>
    <article class="question-card">
        <h1>{{ props.question }}</h1>
        <section class="options">
            <h2 @click="selectOption('A')" :class="[getOptionClass('A'), { selected: selectedOption === 'A' }]">
                <span class="option-letter">A.</span> {{ props.optionA }}
            </h2>
            <h2 @click="selectOption('B')" :class="[getOptionClass('B'), { selected: selectedOption === 'B' }]">
                <span class="option-letter">B.</span> {{ props.optionB }}
            </h2>
            <h2 @click="selectOption('C')" :class="[getOptionClass('C'), { selected: selectedOption === 'C' }]">
                <span class="option-letter">C.</span> {{ props.optionC }}
            </h2>
            <h2 @click="selectOption('D')" :class="[getOptionClass('D'), { selected: selectedOption === 'D' }]">
                <span class="option-letter">D.</span> {{ props.optionD }}
            </h2>
        </section>
        <button class="toggle-button" @click="showAnswer = !showAnswer">
            {{ showAnswer ? 'Hide Answer' : 'Show Answer' }}
        </button>
        <section v-if="showAnswer" class="answer">
            <h2 :class="{ correct: isAnswerCorrect, incorrect: !isAnswerCorrect }">
                {{ isAnswerCorrect ? 'Correct!' : 'Incorrect!' }}
            </h2>
            <button style="background-color: lightseagreen;" class="toggle-button">Explain</button>
        </section>
    </article>
</template>

<script setup>
import { defineProps, ref, computed } from "vue";

const props = defineProps(['question', 'optionA', 'optionB', 'optionC', 'optionD', 'answer'])
const showAnswer = ref(false);
const selectedOption = ref(null);

const isAnswerCorrect = computed(() => {

    if (selectedOption.value == null)
        return false
    // console.log(selectedOption.value.toLowerCase())
    // console.log(props.answer)
    return showAnswer.value && selectedOption.value.toLowerCase() === props.answer;
});

function selectOption(option) {
    if (!showAnswer.value) {
        selectedOption.value = option;
    }
}

function getOptionClass(option) {
    if (showAnswer.value && option === selectedOption.value) {
        console.log(isAnswerCorrect.value)
        return isAnswerCorrect.value ? 'correct' : 'incorrect';
    }
    return '';
}
</script>

<style scoped>
.question-card {
    background-color: #f2f2f2;
    border-radius: 8px;
    padding: 16px;
    margin-bottom: 16px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

h1 {
    font-size: 24px;
    margin-bottom: 16px;
}

.options h2 {
    font-size: 18px;
    margin: 8px 0;
    cursor: pointer;
}

.option-letter {
    font-weight: bold;
    margin-right: 4px;
}

.selected {
    background-color: #e0e0e0;
}

.correct {
    background-color: #d4edda;
    color: #155724;
}

.incorrect {
    background-color: #f8d7da;
    color: #721c24;
}

.toggle-button {
    margin-top: 8px;
    font-size: 16px;
    padding: 8px 16px;
    background-color: #4CAF50;
    color: #fff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.answer {
    margin-top: 16px;
}

.correct {
    color: #4CAF50;
    font-weight: bold;
}

.incorrect {
    color: #f44336;
    font-weight: bold;
}
</style>
