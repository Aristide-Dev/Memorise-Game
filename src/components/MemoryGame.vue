<template>
    <div
      class="flex flex-col md:flex-row border m-0 p-4 bg-gray-300 h-auto min-h-screen"
      :style="{ backgroundColor: currentPlayerColor ?? '#6B7280' }"
    >
      <div class="w-full md:w-1/4 p-4 bg-gray-300">
        <h3 class="text-2xl font-bold mb-6 text-center">Jeu de Mémoire</h3>
  
        <div v-if="showNameInput" class="mb-1">
          <h3 class="text-xl font-semibold mb-4">Entrez les noms des joueurs</h3>
          <div class="flex flex-col space-y-4">
            <div>
              <label class="block mb-2 text-sm font-medium text-gray-900">Joueur 1 :</label>
              <input
                v-model="playerNames[0]"
                type="text"
                class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5"
                placeholder="Nom du Joueur 1"
              />
            </div>
            <div>
              <label class="block mb-2 text-sm font-medium text-gray-900">Joueur 2 :</label>
              <input
                v-model="playerNames[1]"
                type="text"
                class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5"
                placeholder="Nom du Joueur 2"
              />
            </div>
            <button @click="startGame" class="mt-4 px-6 py-2 bg-blue-500 text-white rounded">Commencer le Jeu</button>
          </div>
        </div>
  
        <div v-else>
          <p class="text-lg mb-1">Tour de : <span class="font-bold text-xl" :style="{ color: currentPlayerColor ?? '#6B7280' }">{{ currentPlayerName }}</span></p>
  
          <p class="text-lg font-medium">Niveau : {{ level }}</p>
  
          <div class="mt-6">
            <p class="text-lg font-medium">Scores :</p>
            <ul class="flex justify-between">
              <div class="border w-full">  
                <div class="flex items-center justify-center" :style="{ backgroundColor: playerColors[0] }">
                  <span class="col-span-1 text-white">{{ playerNames[0] }}</span>
                </div>
                <p class="block w-full font-bold text-center text-xl">{{ playerScores[0] }}</p>
              </div>
              <div class="border w-full">  
                <div class="flex items-center justify-center" :style="{ backgroundColor: playerColors[1] }">
                  <span class="col-span-1">{{ playerNames[1] }}</span>
                </div>
                <p class="block w-full font-bold text-center text-xl">{{ playerScores[1] }}</p>
              </div>
            </ul>
          </div>
  
          <div v-if="matchedCards.length === cards.length" class="mt-6">
            <p class="text-2xl font-semibold">Félicitations, {{ winnerName }} a gagné le niveau {{ level }} !</p>
            <button @click="nextLevel" class="mt-4 px-6 py-2 bg-green-500 text-white rounded">Niveau Suivant</button>
          </div>
        </div>
      </div>
  
      <div class="w-full md:w-3/4 p-4 justify-center items-start">
        <div v-if="!showNameInput" :class="gridClass">
          <div v-for="card in cards" :key="card.id" class="relative md:max-w-36 w-full h-20 md:h-52" @click="flipCard(card)">
            <div
              :class="['absolute inset-0 flex items-center justify-center rounded-2xl p-0', card.flipped || card.matched ? 'bg-white' : 'bg-gray-400']"
              :style="card.flipped ? { backgroundImage: `url(${card.image})`, backgroundSize: 'cover' } : { backgroundImage: `url('/mnt/data/card-7031432_1280.png')`, backgroundSize: 'cover', backgroundRepeat: 'no-repeat'}"
            >
              <img v-if="card.flipped || card.matched" :src="card.image" class="w-full h-full object-cover" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        cards: [],
        firstCard: null,
        secondCard: null,
        isChecking: false,
        playerNames: ['Joueur 1', 'Joueur 2'],
        currentPlayerIndex: 0,
        showNameInput: true,
        playerScores: [0, 0],
        playerColors: ['#1E40AF', '#FBBF24'], // Couleurs des joueurs
        level: 1,
      };
    },
    computed: {
      currentPlayerName() {
        return this.playerNames[this.currentPlayerIndex];
      },
      currentPlayerColor() {
        return this.playerColors[this.currentPlayerIndex];
      },
      winnerName() {
        if (this.playerScores[0] > this.playerScores[1]) {
          return this.playerNames[0];
        } else if (this.playerScores[1] > this.playerScores[0]) {
          return this.playerNames[1];
        } else {
          return 'Égalité';
        }
      },
      matchedCards() {
        return this.cards.filter(card => card.matched);
      },
      gridClass() {
        const columns = Math.min(Math.floor(Math.sqrt(this.cards.length)), 6);
        return {
          'grid grid-cols-1 gap-2': columns === 1,
          'grid grid-cols-5 gap-2': columns === 2,
          'grid grid-cols-6 gap-2': columns === 3,
          'grid grid-cols-7 gap-2': columns === 4,
          'grid grid-cols-8 gap-2': columns === 5,
          'grid grid-cols-9 gap-2': columns >= 6,
        };
      },
    },
    methods: {
      startGame() {
        if (this.playerNames[0] && this.playerNames[1]) {
          this.showNameInput = false;
          this.initializeCards();
          this.currentPlayerIndex = this.getRandomPlayer(); // Choisir un joueur aléatoire pour commencer
        } else {
          alert('Veuillez entrer les noms des deux joueurs.');
        }
      },
      getRandomPlayer() {
        return Math.floor(Math.random() * 2); // Retourne 0 ou 1 aléatoirement
      },
      initializeCards() {
        const baseImages = [
            '/cartes_images/a_coeur.svg',
            '/cartes_images/a_treffle.svg',
            '/cartes_images/2_coeur.svg',
            '/cartes_images/3_pique.svg',
            '/cartes_images/5_pique.svg',
            '/cartes_images/6_treffle.svg',
            '/cartes_images/7_carreau.svg',
            '/cartes_images/8_carreau.svg',
            '/cartes_images/9_treffle.svg',
            '/cartes_images/10_carreau.svg',
        ];
  
        const maxPairs = baseImages.length;
        const numberOfPairs = Math.min(3 + this.level, maxPairs);
        const images = baseImages.slice(0, numberOfPairs);
  
        const cards = images.flatMap(image => {
          return [
            { id: image + '1', image, flipped: false, matched: false, matchedByColor: null },
            { id: image + '2', image, flipped: false, matched: false, matchedByColor: null },
          ];
        });
  
        this.cards = this.shuffle(cards);
        this.firstCard = null;
        this.secondCard = null;
        this.isChecking = false;
      },
      shuffle(array) {
        return array.sort(() => Math.random() - 0.5);
      },
      flipCard(card) {
        if (this.isChecking || card.flipped || card.matched) return;
  
        card.flipped = true;
  
        if (!this.firstCard) {
          this.firstCard = card;
        } else {
          this.secondCard = card;
          this.isChecking = true;
          setTimeout(() => {
            if (this.firstCard.image === this.secondCard.image) {
              this.firstCard.matched = true;
              this.secondCard.matched = true;
  
              // Attribuer la couleur du joueur aux cartes
              const playerColor = this.playerColors[this.currentPlayerIndex];
              this.firstCard.matchedByColor = playerColor;
              this.secondCard.matchedByColor = playerColor;
  
              // Incrémenter le score du joueur actuel
              this.playerScores[this.currentPlayerIndex] += 1;
  
              // Laisser le joueur au tour actuel
              this.firstCard = null;
              this.secondCard = null;
              this.isChecking = false;
            } else {
              // Les cartes ne correspondent pas
              this.firstCard.flipped = false;
              this.secondCard.flipped = false;
  
              // Changer de joueur
              this.switchPlayer();
              this.firstCard = null;
              this.secondCard = null;
              this.isChecking = false;
            }
          }, 1000); // Délai de 1 seconde pour retourner les cartes
        }
      },
      switchPlayer() {
        this.currentPlayerIndex = this.currentPlayerIndex === 0 ? 1 : 0; // Changer de joueur
      },
      nextLevel() {
        if (this.level < 10) { // Limiter les niveaux à 10
          this.level += 1;
        }
        this.currentPlayerIndex = this.getRandomPlayer(); // Choisir un joueur aléatoire pour commencer le nouveau niveau
        this.initializeCards(); // Réinitialiser les cartes pour le nouveau niveau
      },
    },
  };
  </script>
  
  <style scoped>
  /* Ajoutez des styles spécifiques si nécessaire */
  </style>
  