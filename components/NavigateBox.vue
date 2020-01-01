<template>
  <div class="answer-box">
    <div class="flex flex-wrap mb-4 content-start">
      <a
        v-for="i in count"
        :class="{
          'answered' : finished || keyList.includes(i - 1),
          'wrong' : finished && incorrectList.includes(i - 1)
        }"
        class="answer-switch rounded bg-gray-300 mx-1 mb-2 cursor-pointer"
        @click="changeQuestion(i - 1)"
        v-text="i"></a>
    </div>
    <div v-if="finished">
      <div class="progress-bar bg-gray-200 mb-3 rounded">
        <span class="procgressing text-sm text-white bg-blue-500 px-5 py-1 block rounded text-right font-bold"
          :style="{width: percent + '%'}">{{ trueAnswersCount }}/{{ count }}</span>
      </div>
    </div>
  </div>
</template>

<script>
Array.prototype.random = function () {
  return this[Math.floor((Math.random()*this.length))];
}

export default {
  props: [
    'quiz',
    'finished',
    'answered',
  ],
  data() {
    return {
      count: this.quiz.length,
      answers: [],
      keyList: [],
      incorrectList: []
    }
  },
  methods: {
    changeQuestion(index) {
      this.$emit('changeQuestion', index);
    },
    updateComponent(index) {
      this.answers = this.answered;
      this.answeredAnswer(index);
      this.incorrectItems();
    },
    answeredAnswer: function(index) {
      if (!this.keyList.includes(index)) this.keyList.push(index);
    },
    incorrectItems: function() {
      this.incorrectList = [];
      if (this.answers) {
        this.quiz.forEach((item, key) => {
          let itemCorrect = item.corrects,
              answersKey = this.answers[key].selected ? this.answers[key].selected : "";
          let compare = (itemCorrect == answersKey);
          if (!compare) this.incorrectList.push(key);
        })
      }
    }
  },
  computed: {
    trueAnswersCount() {
      return this.count - this.incorrectList.length;
    },
    percent() {
      return (this.trueAnswersCount / this.count) * 100;
    }
  },
  created() {
    this.$bus.$on('changedNavigateBox', (index) => {
      this.updateComponent(index);
    })
  }
}
</script>

<style scoped>
.answer-switch {
  display: block;
  text-align: center;
  width: calc(100% / 10 - 0.5rem);
  padding: 5px 0;
  transition: background-color 0.3s ease, color 0.3s ease;
}
.answered {
  background-color: #9bc369;
  color: #fff;
}
.answered.wrong {
  @apply bg-red-500;
}
</style>
