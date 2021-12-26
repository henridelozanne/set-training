<template>
  <div class="indications">
      <div class="score">Score: {{ gameStats.score }}</div>
      <div class="main-info-ctn">
        <div class="grey-boxes-ctn">
          <div class="grey-box">Temps<span>{{ gameStats.totalTime }}s</span></div>
          <div class="grey-box">Cartes restantes<span>{{ piocheCount }}</span></div>
        </div>
        <div class="found">
          <div class="good">Trouvés: <span>{{ gameStats.successCount }}</span></div>
          <div class="bad">Erreurs: <span>{{ gameStats.errorCount }}</span> </div>
        </div>
      </div>
      <div v-if="lastTurnInfo.errorIsVisible" class="reason">Erreur sur {{ reasonItFailed }}</div>
      <div v-else-if="lastTurnInfo.successIsVisible" class="success">Set valide</div>
      <button @click="toggleSolutions" class="see-solutions-btn">?</button>
    </div>
</template>

<script>
export default {
  name: 'Indications',

  props: {
    gameStats: {
      type: Object,
      default: () => {}
    },

    lastTurnInfo: {
      type: Object,
      default: () => {}
    },

    piocheCount: {
      type: Number,
      default: 81,
    },
  },

  computed: {
    averageTime() {
      return Math.round(this.gameStats.totalTime / (this.gameStats.successCount + this.gameStats.errorCount))
    },

    reasonItFailed() {
      switch (this.lastTurnInfo.wrongAttribute) {
        case 'quantity':
          return 'quantité';
        case 'color':
          return 'couleur';
        case 'shape':
          return 'forme';
        case 'fill':
          return 'remplissage';
        default:
          return ''
      }
    },
  },

  emits: ['toggleSolutions'],

  methods: {
    toggleSolutions() {
      this.$emit('toggleSolutions')
    }
  }

}
</script>

<style lang="scss" scoped>
.indications {
  border-radius: 6px;
  background-image: linear-gradient(to right, #434343 0%, black 100%);
  color: white;
  box-shadow: 3px 5px 15px rgba(0, 0, 0, 0.5);
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  position: relative;
  box-sizing: border-box;

  .main-info-ctn {
    width: 100%;
  }

  .score {
    font-size: 2rem;
    margin-bottom: 20px;
  }

  .grey-boxes-ctn {
    > div:not(:first-of-type) {
      margin-top: 10px;
    }

    .grey-box {
      display: flex;
      border-radius: 4px;
      padding: 10px 15px;
      background: #C9CCD3;
      background-image: linear-gradient(-180deg, rgba(255,255,255,0.50) 0%, rgba(0,0,0,0.50) 100%);
      background-blend-mode: lighten;
      color: black;
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      box-sizing: border-box;
  
      span {
        font-weight: bold;
        font-size: 1.1rem;
      }
    }
  }

  .found {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    border-radius: 4px;
    color: white;
    margin-top: 20px;
    overflow: hidden;
    font-weight: bold;
    border-radius: 4px;
    overflow: hidden;
    width: 100%;

    >div {
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 10px 15px;

      span {
        font-size: 1.1em;
        margin-left: 10px;
      }
    }

    .good {
      background: rgb(101, 210, 126);
    }
    
    .bad {
      background: rgb(220, 106, 106);
    }
  }

  .reason {
    border-radius: 4px;
    padding: 10px 15px;
    background: rgb(220, 106, 106);
    color: white;
    margin-top: 20px;
  }

  .success {
    border-radius: 4px;
    padding: 10px 15px;
    background: rgb(101, 210, 126);
    color: white;
    margin-top: 20px;
  }

  .see-solutions-btn {
    position: absolute;
    top: 20px;
    left: 20px;
    height: 30px;
    width: 30px;
    border-radius: 50%;
    padding: 5px;
    cursor: pointer;
    background: white;
  }
}

@media screen and (max-width: 1023px) {
  .indications {
    min-height: 100%;

    .main-info-ctn {
      display: flex;
      height: 100%;
      align-items: center;

      > div {
        width: 50%;
      }
  
      .found {
        margin-left: 10px;
        margin-top: 0;

        > div {
          border-radius: 4px;
        }

        > div:last-of-type {
          margin-top: 10px;
        }
      }
    }
  }
}

@media screen and (max-width: 480px) {
  .indications {
    min-height: unset;
  }
}
</style>