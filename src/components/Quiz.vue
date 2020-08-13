<template>
  <div>
    <h2>Quiz</h2>
    <label><input v-model="onlyCV" type="checkbox" />ボイスありだけに絞る</label><br />
    <button v-on:click="get()">始める</button><br />
    <label><input v-model="hw" type="checkbox" />身長体重も表示</label>
    <div id="quiz"  class="bigText">
      <span>{{ quiz.b }}</span
      > / <span>{{ quiz.w }}</span
      > /
      <span>{{ quiz.h }}</span>
    </div>
    <span v-if="hw">{{ quiz.height }}cm / {{ quiz.weight }} kg</span>
    <h3>Answer</h3>
    <div class="suggestWrap">
    <vue-simple-suggest
      v-model="chosen"
      :list="simpleSuggestionList"
      :filter-by-query="true"
    >
    </vue-simple-suggest>
    </div>
    <button v-on:click="ans()">答え合わせ</button>
    <div>
      <template v-if="ok">
        正解<br />
        <button v-on:click="get()">次の問題</button>
      </template>
      <template v-else>不正解または未回答</template>
    </div><br />
    <button v-on:click="showAnswer ? showAnswer = false : showAnswer = true">
      答えを見る
    </button>
    <div v-if="showAnswer">
      <span class="bigText">{{quiz.answer}}</span><br />
        <button v-on:click="get()">次の問題</button>
    </div>
  </div>
</template>

<script>
import VueSimpleSuggest from 'vue-simple-suggest'
import 'vue-simple-suggest/dist/styles.css'
import data from '../assets/data'

export default {
  name: 'Quiz',
  components: {
    VueSimpleSuggest
  },
  data () {
    return {
      chosen: '',
      showAnswer: false,
      onlyCV: false,
      ok: false,
      hw: false,
      quiz: {
        b: 0,
        w: 0,
        h: 0,
        height: 0,
        weight: 0,
        answer: '',
        answerKana: ''
      }
    }
  },
  methods: {
    get () {
      this.choosenText = ''
      this.chosen = ''
      this.showAnswer = false
      this.ok = false
      const rt = data.data
      for (let i = rt.length - 1; i > 0; i--) {
        const r = Math.floor(Math.random() * (i + 1))
        const tmp = rt[i]
        if (this.onlyCV && (!tmp.CV || !tmp.CV === '')) continue
        rt[i] = rt[r]
        rt[r] = tmp
      }
      const target = rt[0]
      this.quiz.b = target['B']
      this.quiz.w = target['W']
      this.quiz.h = target['H']
      this.quiz.height = target.height
      this.quiz.weight = target.weight
      this.quiz.answer = target.name
      this.quiz.answerKana = target.kana
    },
    ans () {
      const {chosen, quiz} = this
      const exp = quiz.answer + ` (${quiz.answerKana})`
      if (chosen === quiz.answer || chosen === quiz.answerKana || chosen === exp) {
        this.ok = true
      }
    },
    simpleSuggestionList () {
      const ret = data.data
      let idol = []
      for (let i of ret) {
        if (this.onlyCV && (!i.CV || !i.CV === '')) continue
        idol.push(i.name + ` (${i.kana})`)
      }
      return idol
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
button {
  width: 200px;
  height: 30px;
  font-size: 100%;
}
</style>
