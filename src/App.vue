<template>
  <HangmanHeader />
  <div class="game-container">
    <HangmanFigure :wrong-count="wrongLetters.length" />
    <WrongLetters :wrong-letters="wrongLetters" />
    <HangmanWord :letters="letters" :correct-letters="correctLetters" />
  </div>
  <HangmanPopup :status="status" :word="word" @reset="reset" />
  <HangmanNotification :show="notification" />
</template>

<script>
import { computed, ref } from "vue"

import "./assets/style.css";

import HangmanHeader from "./components/HangmanHeader"
import HangmanFigure from "./components/HangmanFigure";
import WrongLetters from "./components/WrongLetters";
import HangmanWord from "./components/HangmanWord";
import HangmanPopup from "./components/HangmanPopup"
import HangmanNotification from "./components/HangmanNotification";

import onKeydown from "./assets/onKeydown";

const words = ["programming", "vuejs", "composition"];
const randomWord = () => words[Math.floor(Math.random() * words.length)];

export default {
  components: { HangmanHeader, HangmanFigure, HangmanWord, WrongLetters, HangmanPopup, HangmanNotification },
  setup() {
    const word = ref(randomWord())
    const guessedLetters = ref([])

    const letters = computed(() => word.value.split(''))
    const wrongLetters = computed(() => 
    guessedLetters.value.filter(l => !letters.value.includes(l))
    )
    const correctLetters = computed(() =>
    guessedLetters.value.filter(l => letters.value.includes(l))
    )

    const status = computed(() => {
      if (wrongLetters.value.length === 6) return 'lose'
      if (letters.value.every(l => correctLetters.value.includes(l)))
      return 'win'
      return ''
    })
    const reset = () => {
      guessedLetters.value = []
      word.value = randomWord()
    }
    const notification = ref(false)
    const showNotification = () => {
      notification.value = true
      setTimeout(() => (notification.value = false), 2000)
    }

    onKeydown(event => {
      const letter = event.key.toLowerCase()
      if (event.keyCode < 65 || event.keyCode > 90) return
      if (status.value) return
      if (guessedLetters.value.includes(letter)) {
        showNotification()
        return
      }
      guessedLetters.value.push(letter)
    })

    return {
      letters,
      HangmanWord,
      wrongLetters,
      correctLetters,
      guessedLetters,
      HangmanNotification,
      status,
      reset
    }
  }
}
</script>

<style></style>
