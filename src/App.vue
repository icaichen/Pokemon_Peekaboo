<template>
  <!-- <h1 class="sr-only">pokemon peek a boo</h1> -->
  <img src="/images/title.png" alt="peek a boo" class="title">
  <section class="description">
    <p>A Card Matching Game with Pokemon Made with Vue.js 3</p>
  </section>
  <transition-group tag="section" class="game-board" name="shuffle-card">
    <Card 
    v-for="card in cardList"
    :key="`${card.value}-${card.variant}`"
    :value="card.value"
    :matched="card.matched"
    :visible="card.visible"
    :position="card.position"
    @select-card="flipCard"

    />
  </transition-group>
  <h2 class="status">{{ status }}</h2>
  <!-- <button @click="shuffleCards">Shuffle Cards</button> -->
  <button v-if="newPlayer" @click="startGame" class="button">
    <img src="/images/go.png" alt="Start Icon"/> Start Game
  </button>
  <button v-else @click="restartGame" class="button">
    <img src="/images/restart.png" alt="Restart Icon"/> Restart Game
  </button>

</template>

<script>
  import _ from 'lodash'
  import {launchConfetti} from './utilities/confetti'
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
      const newPlayer = ref(true)
      
      const startGame = () => {
        newPlayer.value= false

        restartGame()
      }

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
          variant: 1,
          visible: false,
          position: null,
          matched: false
        })
      cardList.value.push({
          value: item,
          variant: 2,
          visible: true,
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

    watch (remainingPairs, currentValue => {
      if (currentValue === 0) {
        launchConfetti()
      }
    })

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
          }, 1000)
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
      restartGame,
      startGame,
      newPlayer
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
  color: white;
  padding-top: 60px;
}

/* #app background-image {
  height: 100%;
} */
.game-board {
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-column-gap: 24px;
  grid-template-rows: repeat(4, 120px);
  grid-row-gap: 24px;
  /* height: 100vh; */
  justify-content: center;
}

.description {
  font-family: 'Oswald', sans-serif;
}
.description p {
  margin: 0;
  font-size: 1.2rem;
  padding-bottom: 30px;
}
.title {
 padding-bottom: 20px; 
}

.button {
  background-color: orange;
  color: white;
  /* padding: 0.75rem 0.5 rem; */
  display: flex;
  align-items: center;
  justify-content: center;
  margin: auto;
  font-weight: bold;
  font-family: 'Oswald', sans-serif;
  padding: 0.7rem 0.5rem;
  border-radius: 10px; 
}

.status {
  font-family: 'Oswald', sans-serif;
}

.button img {
  padding-right: 5px;
}

.shuffle-card-move {
  transition: transform 0.8s ease-in;
}
</style>
