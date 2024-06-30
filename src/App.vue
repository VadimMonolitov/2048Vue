<template>
  <div class="head-container">
    <h1 class="title">2048</h1>
    <div class="score">
      <div class="score-title">Score</div>
      <div class="score-value">{{ score }}</div>
    </div>
    <RestartButton @click="restartGame">New Game</RestartButton>
  </div>
  <div class="game-board">
    <div v-for="cell in grid.flat()" class="cell">
        <div v-if="cell !== 0" :class="['tile', `tile-${cell}`]">
          {{ cell }}
        </div>
      </div>
      <div v-if="isGameOver" class="game-over"> 
        <p>Game over!</p>
        <RestartButton @click="restartGame">Try again</RestartButton>
      </div>
  </div>
</template>


<script>
import RestartButton from "@/components/UI/RestartButton.vue"
export default {
  components: {
    RestartButton
  },
  data() {
    return {
      grid: [
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0]
      ],  
/*         grid: [
        [2, 8, 2, 4],
        [4, 64, 8, 16],
        [32, 8, 1024, 2048],
        [16, 128, 4, 2]
      ],  */
      score: 0,
      isGameOver: false
    };
  },
  methods: {
    addRandomTile() {
      const emptyCells = []

      for (let row = 0; row < this.grid.length; row++) {
        for (let col = 0; col < this.grid[row].length; col++) {
          if (this.grid[row][col] === 0) {
            emptyCells.push({ row, col });
          }
        }
      }
      if (emptyCells.length > 0) {
        const randomIndex = Math.floor(Math.random() * emptyCells.length);
        const { row, col } = emptyCells[randomIndex];

        const newValue = Math.random() < 0.9 ? 2 : 4;

        this.grid[row][col] = newValue;

      }
    },
    handleKeyPress(event) {
      const gridBeforeMove = this.grid.map(row => row.slice());
      switch (event.key) {
        case "ArrowUp":
          this.moveUp()
          break;
        case "ArrowDown":
          this.moveDown();
          break;
        case "ArrowLeft":
          this.moveLeft();
          break;
        case "ArrowRight":
          this.moveRight()
          break;

        default:
          return;
      }
      if (!this.areGridsEqual(gridBeforeMove, this.grid)) {
        this.addRandomTile();
      }
      this.checkGameOver();
    },
    checkGameOver() {
      for (let row = 0; row < this.grid.length; row++) {
        for (let col = 0; col < this.grid[row].length; col++) {
          if (this.grid[row][col] === 0) {
            return; 
          }
        }
      }

      for (let row = 0; row < this.grid.length; row++) {
        for (let col = 0; col < this.grid[row].length; col++) {
          if (col < 3 && this.grid[row][col] === this.grid[row][col + 1]) {
            return; 
          }
          if (row < 3 && this.grid[row][col] === this.grid[row + 1][col]) {
            return; 
          }
        }
      }

      this.isGameOver = true;
    },
    
    areGridsEqual(grid1, grid2) {
      for (let i = 0; i < grid1.length; i++) {
        for (let j = 0; j < grid1[i].length; j++) {
          if (grid1[i][j] !== grid2[i][j]) {
            return false;
          }
        }
      }
      return true;
    },
    moveUp() {
      for (let col = 0; col < 4; col++) {
        let writeIndex = 0;
        for (let row = 0; row < 4; row++) {
          if (this.grid[row][col] !== 0) {
            if (writeIndex > 0 && this.grid[writeIndex - 1][col] === this.grid[row][col]) {
              this.grid[writeIndex - 1][col] *= 2;
              this.score += this.grid[writeIndex - 1][col];
              this.grid[row][col] = 0;
            } else {
              if (writeIndex !== row) {
                this.grid[writeIndex][col] = this.grid[row][col];
                this.grid[row][col] = 0;
              }
              writeIndex++;
            }
          }
        }
      }
    },
    moveDown() {
      for (let col = 0; col < 4; col++) {
        let writeIndex = 3;
        for (let row = 3; row >= 0; row--) {
          if (this.grid[row][col] !== 0) {
            if (writeIndex < 3 && this.grid[writeIndex + 1][col] === this.grid[row][col]) {
              this.grid[writeIndex + 1][col] *= 2;
              this.score += this.grid[writeIndex + 1][col];
              this.grid[row][col] = 0;
            } else {
              if (writeIndex !== row) {
                this.grid[writeIndex][col] = this.grid[row][col];
                this.grid[row][col] = 0;
              }
              writeIndex--;
            }
          }
        }
      }
    },
    moveLeft() {
      for (let row = 0; row < 4; row++) {
        let writeIndex = 0;
        for (let col = 0; col < 4; col++) {
          if (this.grid[row][col] !== 0) {
            if (writeIndex > 0 && this.grid[row][writeIndex - 1] === this.grid[row][col]) {
              this.grid[row][writeIndex - 1] *= 2;
              this.score += this.grid[row][writeIndex - 1]
              this.grid[row][col] = 0;
            } else {
              if (writeIndex !== col) {
                this.grid[row][writeIndex] = this.grid[row][col];
                this.grid[row][col] = 0;
              }
              writeIndex++;
            }
          }
        }
      }
    },
    moveRight() {
      for (let row = 0; row < 4; row++) {
        let writeIndex = 3;
        for (let col = 3; col >= 0; col--) {
          if (this.grid[row][col] !== 0) {
            if (writeIndex < 3 && this.grid[row][writeIndex + 1] === this.grid[row][col]) {
              this.grid[row][writeIndex + 1] *= 2;
              this.score += this.grid[row][writeIndex + 1]
              this.grid[row][col] = 0;
            } else {
              if (writeIndex !== col) {
                this.grid[row][writeIndex] = this.grid[row][col];
                this.grid[row][col] = 0;
              }
              writeIndex--;
            }
          }
        }
      }
    },
    restartGame() {
      this.grid = [
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0]
      ];
      this.score = 0;
      this.isGameOver = false
      this.addRandomTile();
      this.addRandomTile();
    }
  },
  mounted() {
    this.addRandomTile();
    this.addRandomTile();
    window.addEventListener('keydown', this.handleKeyPress)
  }
};
</script>

<style>
.game-board {
    display: grid;
    grid-template-columns: repeat(4, 100px);
    grid-template-rows: repeat(4, 100px);
    gap: 15px;
    background-color: #bbada0;
    border-radius: 6px;
    padding: 15px;
    position: relative;
  }
  
  .cell {
    background-color: #cdc1b4;
    border-radius: 6px;
    position: relative;
    width: 100px;
    height: 100px;
  }
  
  .tile {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    border-radius: 6px;
    font-size: 45px;
    font-weight: bold;
    color: #776e65;
    transition: transform 100ms, opacity 100ms;
    animation: show 200ms ease-out;
  }
  
  .tile-2 { background-color: #eee4da; }
  .tile-4 { background-color: #ede0c8; }
  .tile-8 { background-color: #f2b179; color: #f9f6f2; }
  .tile-16 { background-color: #f59563; color: #f9f6f2; }
  .tile-32 { background-color: #f67c5f; color: #f9f6f2; }
  .tile-64 { background-color: #f65e3b; color: #f9f6f2; }
  .tile-128 { background-color: #edcf72; color: #f9f6f2; }
  .tile-256 { background-color: #edcc61; color: #f9f6f2; }
  .tile-512 { background-color: #edc850; color: #f9f6f2; }
  .tile-1024 { background-color: #edc53f; color: #f9f6f2; }
  .tile-2048 { background-color: #edc22e; color: #f9f6f2; }
  
.head-container {
  display: flex;
  justify-content: right;
  margin-bottom: 20px;
}

.score {
  font-family: "Clear Sans", Arial, sans-serif;
  background: #bbada0;
  font-size: 25px;
  font-weight: bold;
  border-radius: 3px;
  color: white;
  padding: 10px 20px;
  text-align: center;
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.2);
}

.score-title {
  font-size: 18px;
  margin-bottom: 5px;
}

.score-value {
  font-size: 35px;
}

.game-over {
  color: #776e65;
    font-family: "Clear Sans", Arial, sans-serif;
    font-size: 18px;
    cursor: default;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background: rgba(238, 228, 218, 0.73);
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    animation: fade-in 800ms ease 1200ms;
    animation-fill-mode: both;
    display: flex;
}
.game-over>p {
    color: #776e65;
    font-family: "Clear Sans", Arial, sans-serif;
    text-align: center;
    font-size: 60px;
    font-weight: bold;
}

  .title {
    display: flex;
    justify-content: left;
    align-items:center;
    font-size: 40px;
    color: #776e65;
    font-size: 80px;
    font-weight: bold;
    margin: auto;
  }
  @keyframes show {
    0% {
      opacity: 0;
      transform: scale(0);
    }
  }
</style>
