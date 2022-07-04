<template>
  <Header />
  <div class="game-container">
    <Figure 
      :wrong-count="wrongLetters.length" 
    />
    <WrongLetters 
      :wrong-letters="wrongLetters" 
    />
    <Word 
      :letters="letters" 
      :correct-letters="correctLetters" 
    />
  </div>
  <Popup 
    :status="status" 
    :word="word" 
    @reset="reset" 
  />
  <Notification 
    :show="notification" 
  />
</template>

<script>
import { computed, ref } from 'vue'
import onKeydown from '../assets/onKeydown'
import '../assets/style.css'
import Header from './Header.vue'
import Figure from './Figure.vue'
import WrongLetters from './WrongLetters.vue'
import Word from './Word.vue'
import Popup from './Popup.vue'
import Notification from './Notification.vue'

const words = ['programming', 'vuejs', 'composition', 'javascript']
const randomWord = () => words[Math.floor(Math.random() * words.length)]

export default {
  components: {Header, Figure, WrongLetters, Word, Popup, Notification },
  setup() {
    // select a random word to start the game
    const word = ref(randomWord())
    // keep track of letters that we have guessed
    const guessedLetters = ref([])

    // split the random words into letters
    const letters = computed(() => word.value.split(''))
    
    // every time we type a letter these computed functions run and we build up the correct and wrong letters
    const correctLetters = computed(() => guessedLetters.value.filter(letter => letters.value.includes(letter)))
    const wrongLetters = computed(() => guessedLetters.value.filter(letter => !letters.value.includes(letter)))

    // determine if we have won or lost
    const status = computed(() => {
      if(wrongLetters.value.length === 6) {
        return 'lose'
      } else if(letters.value.every(letter => correctLetters.value.includes(letter))) {
        return 'win'
      }
      return ''
    })

    const reset = () => {
      guessedLetters.value = []
      word.value = randomWord()
    }

    const notification = ref(false)

     // show a notification for 2 seconds
    const showNotification = () => {
      notification.value = true
      setTimeout(() => (notification.value = false), 2000)
    }

    // the onKeydown.js file starts a keydown eventlistener on onMounted and removes it on onUnmounted
    onKeydown(event => {
      const letter = event.key.toLowerCase() 
      
      // if we didn't type a letter between a-z or if the game is over, we abort
      if((event.keyCode < 65 || event.keyCode > 90) || status.value) {
        return 
      }

      // if we've guessed a letter that has already been guessed, show the notification and abort
      if(guessedLetters.value.includes(letter)) {
        showNotification()
        return 
      }

      // add the guessed letters to the array
      guessedLetters.value.push(letter)
    })

    return { letters, word, wrongLetters, correctLetters, guessedLetters, notification, status, reset }
  }
}
</script>
