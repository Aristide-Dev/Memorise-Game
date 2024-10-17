<template>
    <div class="flex flex-col items-center mt-8">
      <h1 class="text-3xl font-bold mb-6">Jeu de Morpion</h1>
      <div class="grid grid-cols-3 gap-4">
        <button
          v-for="(cell, index) in cells"
          :key="index"
          class="w-24 h-24 bg-gray-200 flex items-center justify-center text-4xl font-bold"
          @click="makeMove(index)"
        >
          {{ cell }}
        </button>
      </div>
      <div v-if="winner" class="mt-6">
        <p class="text-2xl font-semibold">
          {{ winnerMessage }}
        </p>
        <button @click="resetGame" class="mt-4 px-6 py-2 bg-blue-500 text-white rounded">
          Recommencer
        </button>
      </div>
      <div v-else class="mt-6">
        <p class="text-xl">
          Tour du joueur : <span class="font-bold">{{ currentPlayer }}</span>
        </p>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        cells: Array(9).fill(''),
        currentPlayer: 'X',
        winner: null,
      };
    },
    computed: {
      winnerMessage() {
        return winner === 'Match nul' ? 'Match nul !' : `Le joueur ${winner} a gagné !`;
      },
    },
    methods: {
      makeMove(index) {
        if (!cells[index] && !winner) {
          $set(cells, index, currentPlayer);
          if (checkWin(currentPlayer)) {
            winner = currentPlayer;
          } else if (cells.every(cell => cell)) {
            winner = 'Match nul';
          } else {
            currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
          }
        }
      },
      checkWin(player) {
        const winningCombinations = [
          [0,1,2], [3,4,5], [6,7,8],
          [0,3,6], [1,4,7], [2,5,8],
          [0,4,8], [2,4,6],
        ];
        return winningCombinations.some(combination => {
          return combination.every(index => cells[index] === player);
        });
      },
      resetGame() {
        cells = Array(9).fill('');
        currentPlayer = 'X';
        winner = null;
      },
    },
  };
  </script>
  
  <style scoped>
  /* Styles spécifiques si nécessaire */
  </style>
  