<template>
  <!-- <h1 class="sr-only">pokemon peek a boo</h1> -->
  <img src="/images/title.png" alt="peek a boo" class="title">
  <section class="game-board">
    <Card 
    v-for="(card, index) in cardList"
    :key="`card-${index}`"
    :value="card.value"
    :matched="card.matched"
    :visible="card.visible"
    :position="card.position"
    @select-card="flipCard"

    />
  </section>
  <h2>{{ status }}</h2>
  <!-- <button @click="shuffleCards">Shuffle Cards</button> -->
  <button @click="restartGame" class="button"><img
  src="/images/restart.png" alt="Restart Icon"/> Restart Game
  </button>
</template>

<script>
  import _ from 'lodash'
  import { computed, ref, watch} from 'vue'
  import Card from './components/Card'

  export default {
    name: 'App',
    components: {
      Card
    },
    setup() {
      const cardList = ref([])
      const userSelection = ref([])

      const status = computed(() => {
        if (remainingPairs.value === 0) {
          return 'Player wins!'
        } else {
          return `Remaining Pairs: ${remainingPairs.value}`
        }
      })
 
      const remainingPairs = computed(()=> {
        const remainingCards = cardList.value.filter(
          card => card.matched === false).length

        return remainingCards / 2

      })

      const shuffleCards = () => {
        cardList.value = _.shuffle(cardList.value)
      }
      const restartGame = () => {
        shuffleCards()

        cardList.value = cardList.value.map((card, index) => {
          return {
            ...card,
            matched: false,
            position: index,
            visible: false
          }
        })
      }

    const cardItems =['Charmander', 'pikapika', 'jienijieni', 'meowth', 'zhongzizhongzi', 'cubone', 'Psyduck', 'Lapras']

    cardItems.forEach(item => {
      cardList.value.push({
          value: item,
          visible: false,
          position: null,
          matched: false
        })
      cardList.value.push({
          value: item,
          visible: false,
          position: null,
          matched: false
        })
    }) 

    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index
      }
    })

    const flipCard = payload => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
        if (
          (userSelection.value[0].position === 
          payload.position) && 
          (userSelection.value[0].faceValue === 
          payload.faceValue)
        ) {
          return
        } else {
          userSelection.value[1] = payload
        }
      } else {
          userSelection.value[0] = payload
      }
    }

    watch (
      userSelection, 
      currentValue => {
      if (currentValue.length === 2) {
        const cardOne = currentValue[0]
        const cardTwo = currentValue[1]

        // eslint-disable-next-line no-empty
        if (cardOne.faceValue === cardTwo.faceValue){
          
          cardList.value[cardOne.position].matched = true
          cardList.value[cardTwo.position].matched = true
        } else {
          setTimeout(() => {
            cardList.value[cardOne.position].visible = false
            cardList.value[cardTwo.position].visible = false
          }, 500)
        }

        userSelection.value.length = 0
        }
      },
      { deep: true} 
    )
    return {
      cardList,
      flipCard,
      userSelection,
      status,
      shuffleCards,
      restartGame    
      }
    }
  } 
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
}

h1 {
  margin-top: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-image: url('/images/background.png');
  height: 100vh;
  color: green;
  padding-top: 60px;
}
/* 
#app.background-image {
  object-fit: cover;
} */
.game-board {
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-column-gap: 24px;
  grid-template-rows: repeat(4, 120px);
  grid-row-gap: 24px;
  justify-content: center;
}

.title {
 padding-bottom: 30px; 
}

.button {
  background-color: orange;
  color: white;
  padding: 0.75rem 0.5 rem;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: auto;
  font-weight: bold;
}

.button img {
  padding-right: 5px;
}
</style>
