<template>
  <div>
    <div v-if="currentIndex + 1 !== values.length">
      <input type="file" v-if="values.length === 0" @change="loadFile($event)">
      <div v-if="currentIndex !== null">
        <h3 v-if="values.length !== 0">{{currentIndex + 1}}/{{values.length}}</h3>
        <h4>{{wrongAnswers.length}}</h4>
        <h4>{{correctAnswers.length}}</h4>
        <h1>{{values[currentIndex][0]}}</h1>
        <input type="text" v-model="currentAnswer" @keyup="handleKeyboard($event)">
        <h2>{{correctAnswer}}</h2>
        <button @click="checkAnswer()">{{correctAnswer ? 'Next' : 'Submit'}}</button>
      </div>
    </div>
    <div v-if="currentIndex + 1 === values.length">
      <h3 v-if="values.length !== 0">
        <h2>Result:</h2> {{values.length - wrongAnswers.length}}/{{values.length}}</h3>
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
    data() {
      return {
        currentIndex: null,
        values: [],
        wrongAnswers: [],
        correctAnswers: [],
        currentAnswer: '',
        correctAnswer: null
      }
    },
    methods: {
      loadFile(event) {
        var reader = new FileReader();
        reader.readAsText(event.target.files[0], "UTF-8");
        reader.onload = (readerEvent) => {
          this.values = readerEvent.target.result.split("\n").map((line) => line.split(","));
          if (this.values.length !== 0) {
            this.currentIndex = 0;
          }
        }
      },
      handleKeyboard(event) {
        if (event.keyCode === 13) {
          this.checkAnswer();
        }
      },
      checkAnswer() {
        if (this.correctAnswer === null) {
          if (this.currentAnswer.trim().localeCompare(this.values[this.currentIndex][1].trim()) !== 0) {
            this.wrongAnswers.push(this.currentIndex);
            this.correctAnswer = this.values[this.currentIndex][1];
          } else {
            this.correctAnswers.push(this.currentIndex);
            this.currentAnswer = '';
            this.currentIndex++;
          }
        } else {
          this.currentAnswer = '';
          this.currentIndex++;
          this.correctAnswer = null;
        }
  
      }
    }
  }
</script>

<style scoped>
  ul {
    list-style: none;
  }
</style>