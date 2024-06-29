<template>
  <div class="container mt-5">
    <div class="title">
      <li
        class="left"
        :class="{ hover: !status ? true : false }"
        @click="toggletoLeft"
      >
        🎮 Jouer
      </li>
      <li
        class="right"
        :class="{ hover: status ? true : false }"
        @click="toggletoRight"
      >
        📌 Historiques
      </li>
    </div>
    <div v-if="showGame">
      <h2 v-if="winner" class="text-center text-success joueur mt-3">
        {{ winner }} a gagné ! 🎉🏆
      </h2>
      <h2 v-else class="text-center joueur mt-3">
        {{ avatar }} Joueur : {{ player }}
      </h2>
      <div class="d-flex justify-content-center mb-3">
        <button @click="reset()" class="btn btn-success">🔁 Rejouer</button>
      </div>
      <div class="board">
        <div v-for="x in 3" :key="x" class="board-row">
          <button
            v-for="y in 3"
            :key="y"
            class="square"
            @click="move(x - 1, y - 1)"
          >
            <p>{{ squares[x - 1][y - 1] }}</p>
          </button>
        </div>
      </div>
    </div>
    <div class="histr" v-if="showHistorique">
      <div class="histrHead">
        <h2 class="mt-5 text-center historique">Historiques</h2>
        <button class="btn btn-danger vider" @click="deleteHistorique()">
          🗑️ Supprimer les historiques
        </button>
      </div>
      <div class="history">
        <div v-for="(game, idx) in history" :key="idx">
          ⚔️ Jeux {{ idx + 1 }} : {{ game }} a gagné
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onBeforeMount } from "vue";

const player = ref("❌");
const avatar = ref("👾");
const history = ref([]);
const status = ref(false);
const showGame = ref(true);
const showHistorique = ref(false);
const squares = ref([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
]);

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];

  const flatSquares = squares.flat();

  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (
      flatSquares[a] &&
      flatSquares[a] === flatSquares[b] &&
      flatSquares[a] === flatSquares[c]
    ) {
      return flatSquares[a];
    }
  }
  return null;
}

const winner = computed(() => calculateWinner(squares.value));

function move(x, y) {
  if (winner.value || squares.value[x][y]) return;
  squares.value[x][y] = player.value;
  player.value = player.value === "⭕" ? "❌" : "⭕";
  avatar.value = avatar.value === "🤖" ? "👾" : "🤖";
}

function reset() {
  player.value = "❌";
  avatar.value = "👾";
  if (history.value.length) {
    player.value = history.value[history.value.length - 1];
  }
  squares.value = [
    ["", "", ""],
    ["", "", ""],
    ["", "", ""],
  ];
}

watch(winner, (current, previous) => {
  if (current && !previous) {
    history.value.push(current);
    localStorage.setItem("history", JSON.stringify(history.value));
  }
});

function deleteHistorique() {
  localStorage.removeItem("history");
  history.value = localStorage.getItem("history");
}

onBeforeMount(() => {
  history.value = JSON.parse(localStorage.getItem("history")) ?? [];
  if (history.value.length) {
    player.value = history.value[history.value.length - 1];
  }
});

function toggletoLeft() {
  status.value = false;
  showGame.value = true;
  showHistorique.value = false;
}

function toggletoRight() {
  status.value = true;
  showGame.value = false;
  showHistorique.value = true;
}
</script>

<style scoped src="../styles/style.css"></style>
