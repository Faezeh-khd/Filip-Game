<template>
  <div class="app">
    <header class="header">
      <h1>بازی پیدا کردن محصولات</h1>
    </header>

    <main class="game-container">
      <GameBoard :key="gameKey" @gameStatus="handleStatus" />
      <!--result-->
      <ResultModal
        v-if="showModal"
        :status="status"
        :record="bestRecord"
        @close="showModal = false"
      />
      <div v-if="status !== 'playing'" class="game-result">
        <button @click="restartGame">شروع دوباره</button>
      </div>
    </main>
  </div>
</template>

<script>
import GameBoard from '@/components/GameBoard.vue'
import ResultModal from '@/components/ResultModal.vue'

export default {
  name: 'App',

  components: {
    GameBoard,
    ResultModal,
  },

  data() {
    return {
      gameKey: 0,
      showModal: false,
      status: 'playing', // playing | win | lose

      bestRecord: {
        bestMoves: null,
        bestTime: null,
      },
    }
  },

  methods: {
    restartGame() {
      this.gameKey++
      this.status = 'playing'
    },
    handleStatus(status) {
      this.status = status
      if (status === 'win') {
        this.updateScore()
      }
      this.showModal = true
    },
    updateScore() {
      const bestMoves = Number(localStorage.getItem('bestMoves'))
      const bestTime = Number(localStorage.getItem('bestTime'))

      this.bestRecord = {
        bestMoves: bestMoves,
        bestTime: bestTime,
      }
    },
  },
}
</script>
