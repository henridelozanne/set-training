<template>
  <div class="solutions">
    <div class="title">{{solutions.options.length}} solution{{solutions.options.length > 1 ? 's' : ''}}</div>
    <div v-if="solutions.solutionsVisible" class="button-container">
      <div v-for="(option, index) in solutions.options"
           :key="index"
           class="option"
           @click="optionClicked(option)"
      >
        Solution {{index + 1}}
      </div>
      <div @click="nextTurnAfterReveal" v-if="solutions.solutionRevealed" class="next-button">Suivant</div>
    </div>
    <div v-if="solutions.noSolutionsVisible">
      <div @click="nextTurnAfterNoSolution" class="next-button">Suivant</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Solutions",

  props: {
    solutions: {
      type: Object,
      default: () => {}
    }
  },

  emits: ['optionClicked', 'nextTurnAfterReveal', 'nextTurnAfterNoSolution'],

  methods: {
    optionClicked(option) {
      this.$emit('optionClicked', option)
    },

    nextTurnAfterReveal() {
      this.$emit('nextTurnAfterReveal')
    },

    nextTurnAfterNoSolution() {
      this.$emit('nextTurnAfterNoSolution')
    }
  }
}
</script>

<style lang="scss" scoped>
.solutions {
  border-radius: 6px;
  margin-top: 40px;
  background-image: linear-gradient(to right, #434343 0%, black 100%);
  color: white;
  box-shadow: 3px 5px 15px rgba(0, 0, 0, 0.5);
  display: flex;
  flex-direction: column;
  align-items: center;

  .title {
    font-size: 1.5rem;
  }

  .option, .next-button {
    padding: 10px 20px;
    background: white;
    color: black;
    border-radius: 4px;
    margin-top: 10px;
    cursor: pointer;
  }
  
  .next-button {
    color: white;
    background-color: black;
    display: flex;
    justify-content: center;
    border: 1px solid white;
    transition-property: all;
    transition-duration: 0.5s;

    &:hover {
      color: black;
      background-color: white;
      border: 1px solid black;
    }
  }
}
</style>