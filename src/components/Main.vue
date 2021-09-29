<template>
    <div class="container jumbotron mt-5">
      <h1 class="display-4 text-center">Hızlı Yazma Yarışması</h1>
      <p class="lead text-center font-weight-bolder">Ne kadar hızlı yazdığını test et!</p>
      <div v-if="isFinish" class="alert alert-primary">
        <h3>Süre Bitti!</h3>
        <p class="display-4">{{ dks }} DKS</p>
        <p>Doğruluk Yüzdesi: %{{ truePercent }}</p>
        <p>Doğru Kelime Sayısı: {{ trueCount }}</p>
        <p>Yanlış Kelime Sayısı: {{ falseCount }}</p>
        <button @click="newGame" class="btn btn-success">Yeni Oyun</button>
      </div>
      <div v-else>
      <div class="card">
        <div class="card-body">
          <span
            v-for="(word, key) in words.filter((data, index) => index < 25)"
            :key="key"
            v-bind:class="key != 0 || wordControl"
            class="words"
            >{{ word.toLowerCase() }}</span
          >
        </div>
      </div>
      <div class="card mt-2">
        <div class="card-body">
          <div class="input-group">
            <input type="text" class="form-control" v-model="writingWord"/>
            <div class="input-group-append" id="button-addon4">
              <button disabled class="btn btn-secondary rounded-0" type="button">
                {{ timer }} sn.
              </button>
              <button :disabled="isRunning" class="btn btn-info rounded-0" type="button" @click="getWords()">Yenile
              </button>
              <button class="btn btn-warning rounded-0" type="button" @click="newGame">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-arrow-repeat" viewBox="0 0 16 16">
                  <path d="M11.534 7h3.932a.25.25 0 0 1 .192.41l-1.966 2.36a.25.25 0 0 1-.384 0l-1.966-2.36a.25.25 0 0 1 .192-.41zm-11 2h3.932a.25.25 0 0 0 .192-.41L2.692 6.23a.25.25 0 0 0-.384 0L.342 8.59A.25.25 0 0 0 .534 9z"/>
                  <path fill-rule="evenodd" d="M8 3c-1.552 0-2.94.707-3.857 1.818a.5.5 0 1 1-.771-.636A6.002 6.002 0 0 1 13.917 7H12.9A5.002 5.002 0 0 0 8 3zM3.1 9a5.002 5.002 0 0 0 8.757 2.182.5.5 0 1 1 .771.636A6.002 6.002 0 0 1 2.083 9H3.1z"/>
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
      </div>
    </div>
</template>
<script>
import wordList from '@/assets/words.json'

export default {
  data() {
    return {
      words: [],
      writingWord: null,
      isTrue: true,
      trueCount:0,
      falseCount:0,
      timer: 60,
      interval: false,
      isRunning: false,
      isFinish: false,
      wordList: wordList
    };
  },
  watch: {
    writingWord(val){
      if(!val ||val === ' ') {
        this.writingWord = '';
        return;
      }
      if(!this.isRunning) this.toggleTimer();

      const word = this.words[0].slice(0, val.length);
      const userWord = val.replace(' ','');
      this.isTrue = word.toLowerCase() === userWord;

      if(val.indexOf(' ') !== -1){
        this.isTrue ? this.trueCount++ : this.falseCount++;
        this.words.splice(0,1);
        this.writingWord = '';
        this.isTrue = true;
      }
    }
  },

  mounted() {
    this.getWords();
  },

  computed: {
    wordControl () {
      return this.isTrue ? 'highlight' : 'highlight bg-danger text-white';
    },

    dks() {
      return  this.trueCount;
    },

    truePercent() {
      let percent = (100 / this.dks).toFixed(2);
      let num = (percent * this.trueCount).toFixed(2);
      return isNaN(num) ? 0 : num;
    }
  }, 
  methods: {
    newGame() {
      this.getWords();
      this.isFinish = false;
      this.timer = 60;
      this.isTrue = true;
      this.isRunning = false;
      this.trueCount = 0;
      this.falseCount = 0;
      clearInterval(this.interval);
    },

    getWords() {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0,400).filter((data, i) => data.length <= 8 && data.length >= 2 && i < 400);
    },
    toggleTimer() {
      this.isRunning = true;
      this.interval = setInterval(this.timeProcess, 1000);
    },
    timeProcess(){
      if(this.timer === 0) {
        clearInterval(this.interval);
        this.isFinish = true;
        return;
      }
      this.timer--;
    }
  }
};
</script>

<style>
.jumbotron {
    background: #f1f0ff;
    padding: 20px 15px 30px;
    border-radius: 8px;
}

.display-4 {
    color: #444;
    font-weight: 400 !important;
}

.card-body {
  word-wrap: break-word;
  font-family: 'Times New Roman', Times, serif;
}

button.btn:focus {box-shadow: none !important}
input.form-control {font-size: 20px !important}
.input-group button {font-size: 22px !important}
.form-control:focus {box-shadow: inherit !important}
.btn {border: none !important}
.btn-info {
    background: rebeccapurple !important;
    color: #fff !important;
    font-family: 'Lato', sans-serif !important;
}

.words {
  font-size: 28px;
  font-weight: 500;
  margin-right: 8px;
  white-space: nowrap;
}

.highlight {
  background: #ddd;
  border-radius: 3px;
  padding: 3px 5px;
}
</style>
