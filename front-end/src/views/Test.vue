<template>
  <div>
    <h1>Let's test Your Vocabulary here!</h1>

    <div v-if="!started">
      <p>Click the button below to start testing your vocabulary.</p>
      <button class="ui button primary" @click="startTest">Start the Test</button>
    </div>

    <div v-else>
      <div v-if="word">
        <p><strong>German:</strong> {{ word.german }}</p>

        <div class="ui labeled input fluid">
          <div class="ui label">
            <i class="united kingdom flag"></i> English
          </div>
          <input type="text" placeholder="Enter English translation" v-model="englishAnswer" />
        </div>

        <div class="ui labeled input fluid" style="margin-top: 1rem;">
          <div class="ui label">
            <i class="france flag"></i> French
          </div>
          <input type="text" placeholder="Enter French translation" v-model="frenchAnswer" />
        </div>

        <button class="positive ui button" style="margin-top: 1rem;" @click="checkAnswer">
          Submit
        </button>

        <p v-if="feedback" style="margin-top: 1rem;">{{ feedback }}</p>
      </div>
      <div v-else>
        <p>No words to test. Please add some first.</p>
      </div>
    </div>
  </div>
</template>

<script>
import { api } from '../helpers/helpers';

export default {
  name: 'Test',
  data() {
    return {
      started: false,
      words: [],
      word: null,
      englishAnswer: '',
      frenchAnswer: '',
      feedback: ''
    };
  },
  async mounted() {
    this.words = await api.getWords();
    if (this.words.length > 0) {
      this.word = this.words[Math.floor(Math.random() * this.words.length)];
    }
  },
  methods: {
    startTest() {
      this.started = true;
    },
    checkAnswer() {
      const englishCorrect = this.englishAnswer.trim().toLowerCase() === this.word.english.toLowerCase();
      const frenchCorrect = this.frenchAnswer.trim().toLowerCase() === this.word.french.toLowerCase();

      if (englishCorrect && frenchCorrect) {
        this.feedback = '✅ Both answers are correct!';
      } else if (englishCorrect) {
        this.feedback = '✅ English is correct, but French is incorrect.';
      } else if (frenchCorrect) {
        this.feedback = '✅ French is correct, but English is incorrect.';
      } else {
        this.feedback = `❌ Both are incorrect! Correct answers: English = ${this.word.english}, French = ${this.word.french}`;
      }

      this.englishAnswer = '';
      this.frenchAnswer = '';
    }
  }
};
</script>
