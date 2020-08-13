<template>
  <div id="app">
    <h1>CINDERELLA GIRLS 3SIZE</h1>
    <Quiz />
    <hr />
    <DB />
  </div>
</template>

<script>
import Quiz from './components/Quiz'
import DB from './components/DB'

export default {
  name: 'App',
  components: {
    Quiz,
    DB
  }
}
</script>

<style>
#app {
  font-family: sans-serif;
  color: #2c3e50;
  margin-top: 60px;
}
.suggestWrap {
  max-width: 500px;
}
.bigText {
  font-size: 300%;
}
</style>
