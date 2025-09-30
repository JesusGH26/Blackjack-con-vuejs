<template>
  <div class="blackjack">
    <h1>Blackjack</h1>

    <div class="dealer">
      <h2>Casa</h2>
      <div class="cards">
        <span v-for="(card,i) in dealerCards" :key="i">
          {{ card.display }}
        </span>
      </div>
      <p>Total: {{ dealerTotal }}</p>
    </div>

    <div class="player">
      <h2>Jugador</h2>
      <div class="cards">
        <span v-for="(card,i) in playerCards" :key="i">
          {{ card.display }}
        </span>
      </div>
      <p>Total: {{ playerTotal }}</p>
    </div>

    <div class="controls" v-if="!gameOver">
      <button @click="hit">Pedir (+)</button>
      <button @click="stand">Plantarse</button>
    </div>

    <div class="result" v-if="gameOver">
      <h3>{{ message }}</h3>
      <button @click="startGame">Reiniciar</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BlackjackGame',
  data() {
    return {
      deck: [],
      playerCards: [],
      dealerCards: [],
      gameOver: false,
      message: ''
    }
  },
  computed: {
    playerTotal() {
      return this.sumCards(this.playerCards)
    },
    dealerTotal() {
      return this.sumCards(this.dealerCards)
    }
  },
  methods: {
    initDeck() {
      const suits = ['♠','♥','♦','♣']
      const values = [
        { v: 1,  d: 'A' },
        { v: 2,  d: '2' },
        { v: 3,  d: '3' },
        { v: 4,  d: '4' },
        { v: 5,  d: '5' },
        { v: 6,  d: '6' },
        { v: 7,  d: '7' },
        { v: 8,  d: '8' },
        { v: 9,  d: '9' },
        { v: 10, d: '10' },
        { v: 10, d: 'J' },
        { v: 10, d: 'Q' },
        { v: 10, d: 'K' }
      ]
      this.deck = []
      for (const suit of suits) {
        for (const val of values) {
          this.deck.push({ value: val.v, display: `${val.d}${suit}` })
        }
      }
      for (let i = this.deck.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1))
        ;[this.deck[i], this.deck[j]] = [this.deck[j], this.deck[i]]
      }
    },
    sumCards(cards) {
      let sum = cards.reduce((acc, c) => acc + c.value, 0)
      const aceCount = cards.filter(c => c.value === 1).length
      if (aceCount && sum + 10 <= 21) sum += 10
      return sum
    },
    dealOne(toDealer = false) {
      const card = this.deck.pop()
      if (toDealer) this.dealerCards.push(card)
      else this.playerCards.push(card)
    },
    startGame() {
      this.gameOver = false
      this.message = ''
      this.initDeck()
      this.playerCards = []
      this.dealerCards = []
      this.dealOne()
      this.dealOne()
      this.dealOne(true)
    },
    hit() {
      this.dealOne()
      if (this.playerTotal > 21) {
        this.message = '¡Te pasaste! Pierdes.'
        this.gameOver = true
      } else if (this.playerTotal === 21) {
        this.message = '¡21 exactos! Ganaste.'
        this.gameOver = true
      }
    },
    stand() {
      while (this.dealerTotal < 17) {
        this.dealOne(true)
      }
      if (this.dealerTotal > 21 || this.dealerTotal < this.playerTotal) {
        this.message = 'La casa se pasa o se queda corto. ¡Ganaste!'
      } else {
        this.message = 'La casa gana. ¡Perdiste!'
      }
      this.gameOver = true
    }
  },
  mounted() {
    this.startGame()
  }
}
</script>

<style scoped>
.blackjack {
  max-width: 400px;
  margin: 0 auto;
  text-align: center;
  font-family: sans-serif;
}

.cards span {
  display: inline-block;
  padding: 0.5rem;
  margin: 0.25rem;
  border: 1px solid #333;
  border-radius: 0.5rem;
}

button {
  margin: 0.5rem;
  padding: 0.5rem 1rem;
}
</style>
