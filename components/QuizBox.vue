<template>
  <div class="quiz-container">
    <div class="question-box p-5 rounded-lg relative shadow-lg">
      <div class="quote-icon absolute"></div>
      <div class="text-white text-xl font-semibold relative z-10" v-html="question.question"></div>
      <div class="question-process font-bold mt-2 text-sm opacity-50">No. {{ index + 1 }}/{{ total }}</div>
    </div>
    <div class="answers-box pt-5">
      <a v-for="(value, key) in question.answers"
        :class="{
          'selected' : !finished && value == selectedAnswers,
          'true-answer' : finished ? ((value == selectedAnswers && question.corrects == value)) : '',
          'selected-wrong' : finished ? ((value == selectedAnswers && question.corrects != value)) : '',
          'not-selected-true' : finished ? ((value != selectedAnswers && question.corrects == value)) : '',
        }"
        class="answer-item bg-white py-4 px-5 block rounded-lg mb-5 shadow-lg cursor-pointer font-semibold"
        v-text="value"
        @click="select(value)"></a>
    </div>
  </div>
</template>

<script>
function shuffle(array) {
  var currentIndex = array.length, temporaryValue, randomIndex;
  while (0 !== currentIndex) {
    randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex -= 1;
    temporaryValue = array[currentIndex];
    array[currentIndex] = array[randomIndex];
    array[randomIndex] = temporaryValue;
  }
  return array;
}

export default {
  props: [
    'index',
    'total',
    'question',
    'childAnswer',
    'finished',
  ],
  data() {
    return {
      selectedAnswers: "",
    }
  },
  methods: {
    select(item) {
      if (this.finished) return;
      this.selectedAnswers = item;
      this.$emit('changeAnswered', item);
    },
  },
  watch: {
    question: function() {
      this.selectedAnswers = "";
    },
    childAnswer: function() {
      this.selectedAnswers = this.childAnswer ? this.childAnswer : [];
    }
  },
}
</script>

<style scoped>
.question-box {
  background-color: #9bc369;
}
.quote-icon {
  -webkit-mask: url('~assets/icons/quote-right.svg') no-repeat 100% 100%;
  mask: url('~assets/icons/quote-right.svg') no-repeat 100% 100%;
  -webkit-mask-size: contain;
  mask-size: contain;
  background-color: #92b964;
  width: 48px;
  height: 48px;
  bottom: 20px;
  right: 20px;
}
.answer-item {
  position: relative;
  transition: background-color 0.3s ease, color 0.3s ease, padding-left 0.3s ease;
  border: 2px solid transparent;
}
.answer-item:hover {
  background-color: #f8f8f8;
}
.answer-item.selected {
  border-color: #a0d467;
}
.answer-item::before {
  content: '';
  transform: scale(0) rotate(90deg);
  transition: transform 0.3s ease;
}
.answer-item.not-selected-true {
  @apply bg-yellow-500;
}
.answer-item.selected-wrong {
  @apply bg-red-500;
}
.answer-item.true-answer {
  background-color: #a0d467;
}
</style>
