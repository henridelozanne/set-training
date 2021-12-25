<template>
  <div class="card"
       :class="{'is-selected': card.isSelected, 'is-solution': card.isSolution, 'is-loading-green': isLoadingGreen, 'is-loading-red': isLoadingRed}"
       @click="cardClicked">

      <div class="element"
          v-for="element in card.quantity"
          :key="element"
          :class="`is-${card.color} is-${card.fill} is-${card.shape}`">
      </div>

  </div>
</template>

<script>
export default {
  name: 'Card',

  props: {
    card: {
      type: Object,
      default: () => {}
    },

    index: {
      type: Number,
    },

    lastTurnInfo: {
      type: Object,
      default: () => {}
    }
  },

  data() {
    return {
      isLoading: false,
    }
  },

  computed: {
    isLoadingGreen() {
      return this.isLoading && this.lastTurnInfo.verdict
    },

    isLoadingRed() {
      return this.isLoading && !this.lastTurnInfo.verdict
    }
  },

  watch: {
    card: function () {
      this.isLoading = true
      setTimeout(() => {
        this.isLoading = false
      }, 1500);
    }
  },

  emits: ['card-clicked'],

  methods: {
    cardClicked() {
      this.$emit('card-clicked', {index: this.index, card: this.card})
    }
  }
}
</script>

<style lang="scss">
.card {
  background: white;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.5);
  cursor: pointer;

  &:hover {
    background: rgb(232, 238, 238);
  }

  &.is-selected {
    background: rgb(222, 221, 213);
  }

  &.is-solution {
    background: rgb(133, 210, 255);
  }

  &.is-loading-green {
    background: rgb(101, 210, 126);
    .element {
      background: rgb(101, 210, 126) !important;
      border-color: rgb(101, 210, 126);
    }
  }

  &.is-loading-red {
    background: rgb(220, 106, 106);
    .element {
      background: rgb(220, 106, 106) !important;
      border-color: rgb(220, 106, 106);
    }
  }

  .element {
    height: 50px;
    width: 75px;
    box-sizing: border-box;
  }

  .is-purple {
    background: rgb(92, 8, 92);
    border: 4px solid rgb(92, 8, 92);

    &.is-hatched {
      background: repeating-linear-gradient(
        135deg,
        transparent,
        transparent 3px,
        rgb(92, 8, 92) 3px,
        rgb(92, 8, 92) 6px
      )
    }
  }
  .is-green {
    background: rgb(47, 155, 47);
    border: 4px solid rgb(47, 155, 47);

    &.is-hatched {
      background: repeating-linear-gradient(
        135deg,
        transparent,
        transparent 3px,
        rgb(47, 155, 47) 3px,
        rgb(47, 155, 47) 6px
      )
    }
  }
  .is-orange {
    background: rgb(238, 169, 40);
    border: 4px solid rgb(238, 169, 40);

    &.is-hatched {
      background: repeating-linear-gradient(
        135deg,
        transparent,
        transparent 3px,
        rgb(238, 169, 40) 3px,
        rgb(238, 169, 40) 6px
      )
    }
  }

  // fill
  .is-empty {
    background: transparent;
  }

  // shape
  .is-pill {
    border-radius: 10px;
    width: 50px;
  }
  .is-round {
    border-radius: 50%;
    width: 50px;
  }
  .is-parallelogram {
    // transform: skew(10deg);
    width: 40px;
    height: 40px;
    transform: rotate(45deg)
  }
}
</style>