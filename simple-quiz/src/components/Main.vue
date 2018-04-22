<template>
  <div>
    <div v-if="currentIndex + 1 !== values.length">
      <input type="file" v-if="values.length === 0" @change="loadFile($event)">
      <h3 v-if="values.length !== 0">{{currentIndex + 1}}/{{values.length}}</h3>
      <h1 v-if="currentIndex !== null">{{values[currentIndex][0]}}</h1>
      <input type="text" v-if="currentIndex !== null" @keyup="handleKeyboard($event)">
    </div>
    <div v-if="currentIndex + 1 === values.length">
      <h3 v-if="values.length !== 0"><h2>Result:</h2> {{values.length - wrongAnswers.length}}/{{values.length}}</h3>
      <br>
      <h2>
        Incorrect:  
      </h2>
      <ul>
        <li v-for="valueIndex in wrongAnswers">
          <h3>{{values[valueIndex][0]}} - {{values[valueIndex][1]}}</h3>
        </li>
      </ul>
    </div>
  </div>
  
</template>

<script>
export default {
  name: 'Main',
  data () { return {  currentIndex: null, values: [], wrongAnswers: [] } },
  methods: {
    loadFile(event) {
      var reader = new FileReader();
      reader.readAsText(event.target.files[0], "UTF-8");
      reader.onload = (readerEvent) => {
        this.values = readerEvent.target.result.split("\n").map((line) => line.split(","));
        if (this.values.length !== 0) { this.currentIndex = 0; }
      }
    },
    handleKeyboard(event) {
      if (event.keyCode === 13) { 
        if (this.checkAnswer(event.target.value) !== 0) {
          this.wrongAnswers.push(this.currentIndex);
        }
        event.target.value = '';
        this.currentIndex++;
      }
    },
    checkAnswer(answer) {
      return answer.trim().localeCompare(this.values[this.currentIndex][1].trim());
    }
  }
}
</script>

<style scoped>
  ul {
    list-style: none;
  }
</style>