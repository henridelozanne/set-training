<template>
  <div id="app">
    <div class="board">
      <Card v-for="(card, index) in boardCards"
            :key="index"
            :card="card"
            :index="index"
            :lastTurnInfo="lastTurnInfo"
            @card-clicked="cardClicked"
      />
    </div>
    <div class="right-block">
      <Indications
          :gameStats="gameStats"
          :piocheCount="pioche.length"
          :lastTurnInfo="lastTurnInfo"
          @toggleSolutions="toggleSolutions"
      />
      <Solutions
          v-if="this.solutions.solutionsVisible || this.solutions.noSolutionsVisible"
          :solutions="solutions"
          @optionClicked="optionClicked"
          @nextTurnAfterReveal="nextTurnAfterReveal"
          @nextTurnAfterNoSolution="nextTurnAfterNoSolution"
      />
    </div>
  </div>
</template>

<script>
import Card from './components/Card.vue'
import Indications from './components/Indications.vue'
import Solutions from './components/Solutions.vue'

export default {
  name: 'App',

  components: {
    Card,
    Indications,
    Solutions
  },

  data() {
    return {
      boardCards: [],
      pioche: [],
      cardsClicked: [],
      attributes: ['quantity', 'color', 'shape', 'fill'],
      constants: {
        colors: ['orange', 'purple', 'green'],
        shapes: ['pill', 'round', 'diamond'],
        fill: ['full', 'empty', 'hatched'],
      },
      gameStats: {
        score: 0,
        successCount: 0,
        errorCount: 0,
        totalTime: 0,
      },
      lastTurnInfo: {
        verdict: false,
        errorIsVisible: false,
        successIsVisible: false,
        wrongAttribute: undefined,
      },
      solutions: {
        options: [],
        solutionsVisible: false,
        noSolutionsVisible: false,
        solutionRevealed: false,
      }
    }
  },

  mounted() {
    this.fillPioche()
    this.fillBoard()
  },

  methods: {
    nextTurnAfterNoSolution() {
      this.solutions.noSolutionsVisible = false
      this.setNewTurn([{index: 0}, {index: 1}, {index: 2}])
    },

    nextTurnAfterReveal() {
      this.solutions.solutionsVisible = false
      this.setNewTurn(this.solutions.options.find((option) => option[0].card.isSolution))
      this.solutions.options = []
      this.gameStats.score -= 3
    },

    optionClicked(option) {
      this.boardCards.forEach((card) => card.isSolution = false)
      option.forEach((card) => card.card.isSolution = true)
      this.solutions.solutionRevealed = true
    },

    toggleSolutions() {
      if (!this.solutions.solutionsVisible && !this.solutions.noSolutionsVisible) {
        this.solutions.options = []
        for (let i = 0; i < this.boardCards.length; i += 1) {
          for (let j = i + 1; j < this.boardCards.length; j += 1) {
            for (let k = j + 1; k < this.boardCards.length; k += 1) {
              if (this.checkCards([{card: this.boardCards[i], index: i}, {card: this.boardCards[j], index: j}, {card: this.boardCards[k], index: k}])) {
                this.solutions.options.push([{card: this.boardCards[i], index: i}, {card: this.boardCards[j], index: j}, {card: this.boardCards[k], index: k}])
              }
            }
          }
        }
        if (this.solutions.options.length) {
          this.solutions.solutionsVisible = true
        } else {
          this.solutions.noSolutionsVisible = true
        }
      } else {
        this.solutions.solutionsVisible = false
        this.solutions.noSolutionsVisible = false
      }
    },
    
    cardClicked(card) {
      if (!this.solutions.solutionRevealed) {
        const alreadyClicked = this.cardsClicked.find((cardClicked) => cardClicked.index === card.index)
        if (!alreadyClicked) {
          this.cardsClicked.push(card);
          this.boardCards[card.index].isSelected = true
          if (this.cardsClicked.length === 3) this.checkGuess()
        }
      }
    },

    checkGuess() {
      if (this.checkCards(this.cardsClicked, true)) {
        this.playerWins()
      } else this.playerLoses()
      this.solutions.solutionsVisible = false
      this.solutions.noSolutionsVisible = false
    },

    checkCards(cardsToCheck, showError = false) {
      return this.attributes.every((attribute) => {
        if (cardsToCheck[0].card[attribute] === cardsToCheck[1].card[attribute] && cardsToCheck[1].card[attribute] === cardsToCheck[2].card[attribute]) {
          return true
        } else if (cardsToCheck[0].card[attribute] !== cardsToCheck[1].card[attribute] && 
                   cardsToCheck[0].card[attribute] !== cardsToCheck[2].card[attribute] &&
                   cardsToCheck[1].card[attribute] !== cardsToCheck[2].card[attribute]) {
          return true
        }
        else {
          if (showError) this.lastTurnInfo.wrongAttribute = attribute
          return false
        }
      })
    },

    playerLoses() {
      this.gameStats.score -= 3
      this.lastTurnInfo.verdict = false
      this.gameStats.errorCount += 1
      this.lastTurnInfo.errorIsVisible = true
      setTimeout(() => {
        this.lastTurnInfo.errorIsVisible = false
      }, 1500);
      this.setNewTurn(this.cardsClicked)
      this.cardsClicked = []
    },

    playerWins() {
      this.gameStats.score += 3
      this.lastTurnInfo.verdict = true
      this.gameStats.successCount += 1
      this.lastTurnInfo.successIsVisible = true
      setTimeout(() => {
        this.lastTurnInfo.successIsVisible = false
      }, 1500);
      this.setNewTurn(this.cardsClicked)
      this.cardsClicked = []
    },

    setNewTurn(cardsToReplace) {
      cardsToReplace.forEach((cardToReplace) => {
        const newCardIndex = Math.round(Math.random() * this.pioche.length)
        this.boardCards.splice(cardToReplace.index, 1, this.pioche[newCardIndex])
        this.pioche.splice(newCardIndex, 1)
      })
      this.solutions.solutionRevealed = false
    },

    fillBoard() {
      for (let i = 0; i < 12; i += 1) {
        const randomCardIndex = Math.floor(Math.random() * this.pioche.length)
        this.boardCards.push(this.pioche[randomCardIndex])
        this.pioche.splice(randomCardIndex, 1)
      }
      this.startTotalTime()
    },

    fillPioche() {
      for (let i = 0; i < 3; i += 1) {
        for (let j = 0; j < 3; j += 1) {
          for (let k = 0; k < 3; k += 1) {
            for (let l = 0; l < 3; l += 1) {
              this.pioche.push({
                quantity: i + 1,
                color: this.constants.colors[j],
                shape: this.constants.shapes[k],
                fill: this.constants.fill[l],
                isSelected: false,
                isSolution: false,
              })
            }
          }
        }
      }
    },

    startTotalTime() {
      setInterval(() => {
        this.gameStats.totalTime += 1
      }, 1000);
    },
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@100;400&display=swap');
</style>

<style lang="scss">
body {
  margin: 0;
  height: 100vh;
  width: 100vw;
  padding: 20px;
  background-image: linear-gradient(to right, #6a11cb 0%, #2575fc 100%);
  display: flex;
  align-items: center;
  box-sizing: border-box;
}

#app {
  display: flex;
  width: 100%;
  height: 100%;
  align-items: center;
  justify-content: space-around;
  position: relative;
  font-family: 'Montserrat', sans-serif;
  box-sizing: border-box;

  .board {
    padding: 50px;
    width: 70%;
    max-width: 800px;
    height: 100%;
    border-radius: 6px;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    grid-template-rows: 1fr 1fr 1fr;
    gap: 15px;
    background-image: linear-gradient(to right, #434343 0%, black 100%);
    box-shadow: 2px 5px 15px rgba(0, 0, 0, 0.5);
    box-sizing: border-box;
  }

  .right-block {
    align-self: flex-start;
    width: 20%;
    min-width: 230px;
    max-width: 500px;
    box-sizing: border-box;

    > div {
      padding: 20px 40px;
    }
  }
}

@media screen and (max-width: 1023px) {
  #app {
    flex-direction: column;

    .board {
      width: 100%;
      height: 650px;
    }

    .right-block {
      align-self: unset;
      width: 100%;
      margin-top: 10px;
      display: flex;

      > div {
        padding: 10px 15px;
      }

      .solutions {
        margin-top: 0;
        min-width: 180px;
        margin-left: 10px;
      }
    }
  }
}

@media screen and (max-width: 480px) {
  body {
    padding: 0;
    height: unset;

    #app {
      height: unset;

      .board {
        height: 100vh;
        padding: 20px 10px;
        grid-template-columns: 1fr 1fr 1fr;
        grid-template-rows: 1fr 1fr 1fr 1fr;
        gap: 5px;
      }

      .right-block {
        flex-direction: column;

        > div {
          height: unset;
          min-height: unset;
        }

        .solutions {
          margin-left: 0;
          margin-top: 10px;
        }
      }
    }
  }
}
</style>
