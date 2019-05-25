<template>
  <div class="container">
    <div v-if="currentIndex + 1 !== values.length">
      <form v-if="values.length === 0">
        <fieldset>
          <label for="loadFile">Exercise file</label>
          <input type="file" id="loadFile"  @change="loadFile($event)">
          <label for="shuffle"> Shuffle</label>
          <input type="checkbox" id="shuffle" v-model="shuffle">
          <label for="invert"> Invert</label>
          <input type="checkbox" id="invert" v-model="invert">
        </fieldset>
      </form>
      <div v-if="currentIndex !== null">
        <h3 v-if="values.length !== 0">{{currentIndex + 1}}/{{values.length}}</h3>
        <h4>Wrong: {{wrongAnswers.length}}</h4>
        <h4>Correct: {{correctAnswers.length}}</h4>
        <h1>{{values[currentIndex][0]}}</h1>
        <input type="text" v-model="currentAnswer" @keyup="handleKeyboard($event)">
        <button @click="checkAnswer()">{{correctAnswer ? 'Next' : 'Submit'}}</button>
        <h2>{{correctAnswer}}</h2>
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
        correctAnswer: null,
        shuffle: Boolean,
        invert: Boolean
      }
    },
    methods: {
      loadFile(event) {
        var reader = new FileReader();
        reader.readAsText(event.target.files[0], "UTF-8");
        reader.onload = (readerEvent) => {
          this.values = readerEvent.target.result.split("\n").map((line) => {
            var questionAndAnswer;
            if (line.indexOf(",") !== -1) {
              questionAndAnswer = line.split(",");
            } else {
              questionAndAnswer = line.split(";");
            }
            if (this.invert) {
              return questionAndAnswer.reverse();  
            } else{
              return questionAndAnswer;
            }
          });
          if (this.values.length !== 0) {
            this.currentIndex = 0;
          }
          if (this.shuffle) {
            for (let i = this.values.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [this.values[i], this.values[j]] = [this.values[j], this.values[i]];
            }
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