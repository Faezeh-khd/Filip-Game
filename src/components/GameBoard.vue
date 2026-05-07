<template>
  <div class="board">
    <div class="game-info">
      <div>تعداد حرکت باقی مانده: {{ moves }}</div>
      <div>زمان: {{ time }} ثانیه</div>
    </div>

    <div class="grid">
      <FlipCard
        v-for="card in cards"
        :key="card.id"
        :card="card"
        :class="{ disabled: gameOver }"
        @click="handleCardClick(card)"
      />
    </div>
  </div>
</template>

<script>
import FlipCard from './FlipCard.vue'

export default {
  name: 'GameBoard',

  components: {
    FlipCard,
  },

  data() {
    return {
      time: 120,
      moves: 40,
      gameOver: false,

      cards: [],
      flippedCards: [],

      timerStarted: false,
      timer: null,

      images: Object.values(
        import.meta.glob('@/assets/images/*.jpg', {
          eager: true,
          import: 'default',
        }),
      ),
    }
  },

  methods: {
    shuffle(array) {
      const imageArray = [...array]

      for (let i = imageArray.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1))
        ;[imageArray[i], imageArray[j]] = [imageArray[j], imageArray[i]]
      }

      return imageArray
    },

    startGame() {
      const duplicatedImages = [...this.images, ...this.images]

      const shuffledCards = this.shuffle(duplicatedImages).map((img, index) => ({
        id: index,
        image: img,
        flipped: false,
        matched: false,
      }))

      this.cards = shuffledCards
    },

    startTimer() {
      if (this.timerStarted) return

      this.timerStarted = true

      this.timer = setInterval(() => {
        this.time--

        if (this.time <= 0) {
          clearInterval(this.timer)
          this.gameOver = true
          this.$emit('gameStatus', 'lose')
        }
      }, 1000)
    },

    handleCardClick(card) {
      if (this.gameOver) return
      if (card.flipped || card.matched) return
      if (this.flippedCards.length === 2) return

      this.startTimer()

      card.flipped = true
      this.flippedCards.push(card)
      this.moves--

      if (this.flippedCards.length === 2) {
        const [first, second] = this.flippedCards

        if (first.image === second.image) {
          first.matched = true
          second.matched = true
          this.flippedCards = []
          this.checkIsWin()
        } else {
          setTimeout(() => {
            first.flipped = false
            second.flipped = false
            this.flippedCards = []
          }, 1000)
        }
        if (this.moves === 0 && !this.gameOver) {
          this.gameOver = true
          clearInterval(this.timer)
          this.$emit('gameStatus', 'lose')
        }
      }
    },

    checkIsWin() {
      const allMatched = this.cards.every((card) => card.matched)

      if (allMatched) {
        clearInterval(this.timer)
        this.gameOver = true
        this.saveScore()
        this.$emit('gameStatus', 'win')
      }
    },
    saveScore() {
      const usedMoves = 40 - this.moves
      const usedTime = 120 - this.time

      const bestMoves = Number(localStorage.getItem('bestMoves'))
      const bestTime = Number(localStorage.getItem('bestTime'))

      if (!bestMoves || usedMoves < bestMoves) {
        localStorage.setItem('bestMoves', usedMoves)
      }

      if (!bestTime || usedTime < bestTime) {
        localStorage.setItem('bestTime', usedTime)
      }
    },
  },

  mounted() {
    this.startGame()
  },
}
</script>
