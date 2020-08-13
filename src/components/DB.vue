<template>
  <div>
    <h2>DB</h2>
    <h3>IDOL to 3size</h3>
    <div class="suggestWrap">
    <vue-simple-suggest
      v-model="chosen"
      :list="simpleSuggestionList"
      :filter-by-query="true"
    >
    </vue-simple-suggest>
    </div>
    <button v-on:click="getIt3()">Check</button>
    <div class="bigText">
      <span>{{ it3.b }}</span> / <span>{{ it3.w }}</span> /
      <span>{{ it3.h }}</span>
    </div>
    (height: {{ it3.height }} / weight: {{ it3.weight }})
    <h3>3size to IDOL</h3>
    空欄値はなかったことにします。<br />
    <label><input v-model="det" type="checkbox" />詳細データも表示</label><br />
    <input type="text" v-model="b" placeholder="B" class="tti-text" />
    <input type="text" v-model="w" placeholder="W" class="tti-text" />
    <input type="text" v-model="h" placeholder="H" class="tti-text" />
    <button v-on:click="calc()">Calc</button>
    <span v-if="det"><br />d: 入力値との距離/ RrI: ローレル指数</span>
    <table>
      <tr>
        <td style="width: 8rem; text-align:right">Name(age)</td>
        <td style="width: 3rem">B</td>
        <td style="width: 2rem">W</td>
        <td style="width: 2rem">H</td>
        <td style="width: 3rem" v-if="det">d</td>
        <td style="width: 3rem">He</td>
        <td style="width: 2rem">We</td>
        <td style="width: 3rem" v-if="det">BMI</td>
        <td style="width: 5rem" v-if="det">RrI</td>
      </tr>
      <tr v-for="i in table" :key="i.id">
        <td style="text-align:right">
          <b>{{ i.name }}</b>({{ i.age }})
        </td>
        <td>{{ i.b }}</td>
        <td>{{ i.w }}</td>
        <td>{{ i.h }}</td>
        <td v-if="det">{{ Math.floor(Math.pow(i.diff, 0.5) * 10) / 10 }}</td>
        <td>{{ i.height }}</td>
        <td>{{ i.weight }}</td>
        <td v-if="det">{{ i.bmi }}</td>
        <td v-if="det">{{ i.r }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
import VueSimpleSuggest from 'vue-simple-suggest'
import 'vue-simple-suggest/dist/styles.css'
import data from '../assets/data'

export default {
  name: 'DB',
  components: {
    VueSimpleSuggest
  },
  data () {
    return {
      chosen: '',
      it3: {
        b: 0,
        w: 0,
        h: 0,
        height: 0,
        weight: 0
      },
      b: null,
      w: null,
      h: null,
      det: true,
      table: []
    }
  },
  methods: {
    getIt3 () {
      this.it3.b = 0
      this.it3.w = 0
      this.it3.h = 0
      const ret = data.data
      const { chosen } = this
      for (let i of ret) {
        const exp = i.name + ` (${i.kana})`
        if (chosen === i.name || chosen === i.kana || chosen === exp) {
          this.it3.b = i['B']
          this.it3.w = i['W']
          this.it3.h = i['H']
          this.it3.height = i.height
          this.it3.weight = i.weight
          break
        }
      }
    },
    calc () {
      const { b, w, h } = this
      const ret = data.data
      let table = []
      let mode = 0
      if (b && w && !h) mode = 1
      if (b && !w && h) mode = 2
      if (!b && w && h) mode = 3
      if (b && !w && !h) mode = 4
      if (!b && w && !h) mode = 5
      if (!b && !w && h) mode = 6
      for (let i of ret) {
        let diff = 0
        if (!parseInt(i['B'])) continue
        if (mode === 0) {
          diff =
            Math.pow(b - i['B'], 2) +
            Math.pow(w - i['W'], 2) +
            Math.pow(h - i['H'], 2)
        }
        if (mode === 1) { diff = Math.pow(b - i['B'], 2) + Math.pow(w - i['W'], 2) }
        if (mode === 2) { diff = Math.pow(b - i['B'], 2) + Math.pow(h - i['H'], 2) }
        if (mode === 3) { diff = Math.pow(w - i['W'], 2) + Math.pow(h - i['H'], 2) }
        if (mode === 4) diff = Math.pow(b - i['B'], 2)
        if (mode === 5) diff = Math.pow(w - i['W'], 2)
        if (mode === 6) diff = Math.pow(h - i['H'], 2)
        table.push({
          id: i['ID'],
          name: i.name,
          diff: diff,
          b: i['B'],
          w: i['W'],
          h: i['H'],
          height: i.height,
          weight: i.weight,
          age: i.age,
          bmi: Math.floor((i.weight / Math.pow(i.height / 100, 2)) * 10) / 10,
          r: Math.floor((i.weight / Math.pow(i.height / 100, 3)) * 100) / 10
        })
      }
      table.sort(function (a, b) {
        if (a.diff < b.diff) return -1
        if (a.diff > b.diff) return 1
        return 0
      })
      this.table = table.slice(0, 30)
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
.bigText {
  font-size: 300%;
}
button {
  width: 200px;
  height: 30px;
  font-size: 100%;
}
.tti-text {
  width: 30%;
  max-width: 160px;
  height: 30px;
}
</style>
