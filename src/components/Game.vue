<template>
  <div class="page-wrapper game">
    <div>
      <div>
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
                      :disabled="checkLetterPressed(key)">
                {{ key }}
              </button>
            </template>
          </div>
        </template>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Game',
  data() {
    return {
      chancesLeft: 6,
      initialWord: 'qwerty',
      hiddenWord: [],
      word: [],
      pressedKey: '',
      pressedKeys: [],
      keyboard: ['qwertyuiop'.split(''), 'asdfghjkl'.split(''), 'zxcvbnm'.split('')],
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
      this.hiddenWord = this.initialWord.split('');
    },
    checkLetterPressed(key) {
      return this.pressedKeys.find(item => item === key);
    },
    getGameStatus() {
      if (this.chancesLeft) {
        return `Chances left: ${this.chancesLeft}`;
      } else if (this.chancesLeft && this.pressedKeys) {
        return 'Yes'; // TODO: FINISH
      } else if (this.chancesLeft === 0) {
        return 'You lose';
      }
    },
    pushLetter(letter) {
      if (this.checkLetterPressed(letter)) {
        return;
      }
      if (!this.hiddenWord.find(item => item === letter)) {
        this.chancesLeft = this.chancesLeft - 1;
      }
      this.pressedKey = letter;
      this.pressedKeys.push(letter);
    },
  },
};
</script>
