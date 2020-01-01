<template>
  <div class="quiz-box mx-auto md:mt-10 p-5 rounded-lg">
    <transition name="fade" mode="out-in">
      <div v-if="!ready" key="1">
        <h1 class="text-center uppercase text-3xl font-bold" v-text="content.title"></h1>
        <div class="py-5"></div>
        <div class="text-center">
          <button class="button bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded" @click="ready = !ready" v-text="content.start"></button>
        </div>
      </div>
      <div v-if="ready" key="2">
        <navigate-box
          :quiz="quiz"
          @changeQuestion="changeQuestion"
          :answered="answered"
          :finished="finished" />
        <quiz-box
          :question="quiz[index]"
          :index="index"
          :total="quiz.length"
          :finished="finished"
          @changeAnswered="changeAnswer"
          :childAnswer="childAnswer" />
        <div v-if="finished" class="px-2 py-4 text-center font-semibold text-red-700" v-text="quiz[index].note"></div>
        <div class="flex content-between">
          <div class="flex-auto text-left">
            <button class="button bg-green-500 hover:bg-green-600 text-coal py-3 px-5 rounded shadow-lg"
              @click="submit" v-if="!finished" v-text="content.finish"></button>
            <button class="button bg-green-500 hover:bg-green-600 text-coal py-3 px-5 rounded shadow-lg"
              @click="reset" v-if="finished" v-text="content.reset"></button>
          </div>
          <div class="flex-auto text-right navigate-button">
            <button class="button bg-gray-500 hover:bg-gray-600 text-white py-3 px-6 rounded shadow-lg" data-to="prev"
              @click="prev" v-if="index != 0"></button>
            <button class="button bg-yellow-500 hover:bg-yellow-600 text-coal py-3 px-6 rounded shadow-lg" data-to="next"
              @click="next" v-if="index != quiz.length - 1"></button>
          </div>
        </div>
        <div class="explain p-5" v-if="finished">
          <div class="m-2 text-sm"><span class="square rounded mr-1 bg-green"></span> {{ content.answer.selected_true }}</div>
          <div class="m-2 text-sm"><span class="square rounded mr-1 bg-red-500"></span> {{ content.answer.selected_wrong }}</div>
          <div class="m-2 text-sm"><span class="square rounded mr-1 bg-yellow-500"></span> {{ content.answer.not_selected_true }}</div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import QuizBox from '@/components/QuizBox'
import NavigateBox from '@/components/NavigateBox'
import questions from '~/static/questions.json'

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
  components: {
    QuizBox,
    NavigateBox,
  },
  data() {
    return {
      content: {
        title: 'NuxtJS Quiz',
        start: 'Start',
        finish: 'Finish',
        reset: 'Play Again',
        answer: {
          selected_true: 'True answer',
          selected_wrong: 'Chosen but wrong',
          not_selected_true: 'True, but not select'
        }
      },
      ready: false,
      finished: false,
      shuffleable: false,
      index: 0,
      defaultQuiz: [],
      answered: this.quiz,
      childAnswer: undefined,
    }
  },
  methods: {
    prev() {
      if (this.index == 0) return;
      this.index--;
      this.changeQuestion(this.index);
    },
    next() {
      if (this.index == (this.quiz.length - 1)) return;
      this.index++;
      this.changeQuestion(this.index);
    },
    submit() {
      this.finished = true;
      this.$bus.$emit('changedNavigateBox', this.answered);
    },
    reset() {
      this.ready = false;
      this.finished = false;
      this.index = 0;
      this.childAnswer = undefined;
      this.quiz = this.shuffleable ? shuffle(this.defaultQuiz) : this.defaultQuiz;
    },
    changeQuestion(index) {
      this.index = index;
      this.childAnswer = this.answered[this.index].selected ? this.answered[this.index].selected : undefined;
    },
    changeAnswer(item) {
      this.answered[this.index].selected = item;
      this.$bus.$emit('changedNavigateBox', this.index);
    },
  },
  computed: {
    quiz() {
      return this.shuffleable ? shuffle(this.defaultQuiz) : this.defaultQuiz;
    }
  },
  asyncData({params}) {
    return {
      defaultQuiz: questions,
      answered: questions
    }
  },
  mounted() {
    let app = this;
    window.addEventListener('keyup', function(event) {
      if (app.ready && event.keyCode === 37) app.prev();
      if (app.ready && event.keyCode === 39) app.next();
    })
  },
}
</script>

<style scoped>
.quiz-box {
  max-width: 640px;
}
.button {
  transition: background-color 0.3s ease;
}
.explain .square {
  width: 24px;
  height: 24px;
  display: inline-block;
  vertical-align: middle;
}
.bg-green {
  background-color: #a0d467;
}
.navigate-button button {
  height: 100%;
  position: relative;
}
.navigate-button button::before {
  content: '';
  display: block;
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  -webkit-mask: url('~assets/icons/angle-right-solid.svg') no-repeat;
  mask: url('~assets/icons/angle-right-solid.svg') no-repeat;
  mask-position: center;
  background-color: #1c1c1c;
}
.navigate-button button[data-to="prev"]::before {
  -webkit-mask: url('~assets/icons/angle-left-solid.svg') no-repeat;
  mask: url('~assets/icons/angle-left-solid.svg') no-repeat;
  mask-position: 46% center;
}
.navigate-button button::before,
.navigate-button button[data-to="prev"]::before {
  -webkit-mask-size: 24%;
  mask-size: 24%;
}
</style>
