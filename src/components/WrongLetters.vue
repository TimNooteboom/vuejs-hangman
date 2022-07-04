<template>
  <div class="wrong-letters-container">
    <div class="wrong-letters">
      <p v-show="wrongLetters.length">Wrong</p>
      <span v-for="(letter, i) in wrongLetters" :key="letter">
        <!-- display a list of wrong letters to the user. Add a comma after each letter except the last -->
        {{ letter }}
        {{ i === wrongLetters.length - 1 ? '' : ', ' }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    wrongLetters: { 
      type: Array, 
      default: () => [] 
    }
  }
}
</script>