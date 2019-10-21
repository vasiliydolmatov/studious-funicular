<template>
  <div class="page-wrapper game">
   <div class="hangman">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
        width="350px"
        height="275px"
        viewBox="0 0 350 300"
        preserveAspectRatio="xMidYMid meet"
      >
        <line
          v-if="chancesLeft < 10"
          x1="80"
          y1="257"
          x2="260"
          y2="257"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <line
          v-if="chancesLeft < 9"
          x1="100"
          y1="257"
          x2="100"
          y2="40"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <line
          v-if="chancesLeft < 8"
          x1="100"
          y1="40"
          x2="230"
          y2="40"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <line
          v-if="chancesLeft < 7"
          x1="230"
          y1="40"
          x2="230"
          y2="80"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <circle
          v-if="chancesLeft < 6"
          cx="230"
          cy="90"
          style="fill:khaki;stroke:black;stroke-width:2px;"
          r="20"
        />
        <line
          v-if="chancesLeft < 5"
          x1="230"
          y1="110"
          x2="230"
          y2="170"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <line
          v-if="chancesLeft < 4"
          x1="230"
          y1="140"
          x2="250"
          y2="120"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <line
          v-if="chancesLeft < 3"
          x1="230"
          y1="140"
          x2="210"
          y2="120"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <line
          v-if="chancesLeft < 2"
          x1="230"
          y1="170"
          x2="250"
          y2="200"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
        <line
          v-if="chancesLeft < 1"
          x1="230"
          y1="170"
          x2="210"
          y2="200"
          style="stroke:black;fill:none;stroke-width:2px;"
        />
      </svg>
     </div>
      <div class="counter">
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
              <button
                v-bind:key="idx1"
                class="key"
                v-shortkey.once="[key]"
                @shortkey="pushLetter(key)"
                @click="pushLetter(key)"
                :disabled="checkLetterPressed(key) || (lose || won)"
              >{{ key }}</button>
            </template>
          </div>
        </template>
      </section>
      <div class="footer">
        <button
          v-if="lose || won"
          class="button-restart"
          @click="restartGame()"
        >{{lose ? "New game" : "Continue"}}</button>
      </div>
    </div>
</template>

<script>
const words = ['facebook', 'github', 'cake'];

export default {
  name: 'Game',
  data() {
    return {
      chancesLeft: 10,
      initialWord: '',
      hiddenWord: [],
      pressedKey: '',
      pressedKeys: [],
      keyboard: [
        'qwertyuiop'.split(''),
        'asdfghjkl'.split(''),
        'zxcvbnm'.split(''),
      ],
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
      const success = this.hiddenWord.every(val =>
        this.pressedKeys.includes(val),
      );
      if (success && this.pressedKey) {
        this.won = true;
        return 'You Won!';
      }

      if (this.chancesLeft) {
        return `Chances left: ${this.chancesLeft}`;
      }
      this.lose = true;
      return 'You Lose!';
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
      this.getGameStatus();
    },
    restartGame() {
      this.prepareWord();
      this.lose = false;
      this.won = false;
      this.pressedKey = '';
      this.pressedKeys = [];
      this.chancesLeft = 10;
    },
  },
};
</script>
