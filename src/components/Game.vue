<template>
  <div class="page-wrapper game">
    <div>
      <div class="header">
        <h2>{{getGameStatus()}}</h2>
      </div>
      <div class="letters-wrapper">
        <template v-for="(letter, idx) in hiddenWord">
          <div v-bind:key="idx" :class="{letter: true, blankspace: letter === ' '}">
            <h2>{{checkLetterPressed(letter) ? letter : ''}}</h2>
          </div>
        </template>
      </div>

      <section class="keyboard-wrapper" id="keyboard">
        <template v-for="(keys, idx) in keyboard">
          <div v-bind:key="idx" class="keyLine">
            <template v-for="(key, idx1) in keys">
              <button v-bind:key="idx1"
                      class="key"
                      v-shortkey.once="[key]"
                      @shortkey="pushLetter(key)" @click="pushLetter(key)"
                      :disabled="checkLetterPressed(key) || (lose || won)">
                {{ key }}
              </button>
            </template>
          </div>
        </template>
          <button v-if="lose || won" class="key-restart" @click="restartGame()">{{lose ? 'Try again' : 'Play new game'}}</button>
      </section>
    </div>
  </div>
</template>

<script>
const words = ["facebook", "github", "cake"];

export default {
  name: 'Game',
  data() {
    return {
      chancesLeft: 6,
      initialWord: '',
      hiddenWord: [],
      pressedKey: '',
      pressedKeys: [],
      keyboard: ['qwertyuiop'.split(''), 'asdfghjkl'.split(''), 'zxcvbnm'.split('')],
      lose: false,
      won: false,
    };
  },
  mounted() {
    this.setup();
  },
  methods: {
    setup() {
      this.prepareWord();
    },
    prepareWord() {
      const random = Math.floor(Math.random() * words.length);
      this.initialWord = words[random];
      this.hiddenWord = this.initialWord.split('');
    },
    checkLetterPressed(key) {
      return this.pressedKeys.find(item => item === key);
    },
    getGameStatus() {
      const success = this.hiddenWord.every((val) => this.pressedKeys.includes(val));
     
     if (success && this.pressedKey) {
        this.won = true;
        return 'You Won!'; 
      };

      if (this.chancesLeft) {
        return `Chances left: ${this.chancesLeft}`;
      } else {
          this.lose = true;
        return 'You Lose!';
      };
    },
    pushLetter(letter) {
      if (this.won || this.lose) return;
      if (this.checkLetterPressed(letter)) {
        return;
      }
      if (!this.hiddenWord.find(item => item === letter)) {
        this.chancesLeft = this.chancesLeft - 1;
      }
      this.pressedKey = letter;
      this.pressedKeys.push(letter);
      this.getGameStatus()
    },
    restartGame() {
    this.prepareWord();
    this.lose = false;
    this.won = false;
    this.pressedKey = '';
    this.pressedKeys = [];
    this.chancesLeft = 6;
    }
  },
};
</script>
