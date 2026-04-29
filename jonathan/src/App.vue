<template>
  <div class="game-container">

    <!-- LEFT: Preview -->
    <div class="sidebar">
      <h3>Next Levels</h3>

      <div
        v-for="(img, index) in images"
        :key="index"
        class="preview"
        :class="{ active: index === currentLevel }"
      >
        <img :src="img" alt="preview" />
        <span>Level {{ index + 1 }}</span>
      </div>
    </div>

    <!-- MAIN GAME -->
    <div class="game">
      <h1>🧠 Rwandan Museum Puzzle</h1>
      <h3>Level {{ currentLevel + 1 }} / {{ images.length }}</h3>

      <div class="puzzle">
        <div
          v-for="(tile, index) in tiles"
          :key="index"
          class="tile"
          :class="{ empty: tile === 0 }"
          :style="getTileStyle(tile)"
          @click="moveTile(index)"
        >
          <span v-if="tile !== 0">{{ tile }}</span>
        </div>
      </div>

      <p class="message">{{ message }}</p>
      <button @click="startGame">Restart</button>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

/* ✅ IMPORT IMAGES PROPERLY */
import img1 from "Capture.PNG";
import img2 from "";
import img3 from "";
import img4 from "";
import img5 from "";
import img6 from "";
import img7 from "";
import img8 from "";
import img9 from "";
import img10 from "";

const images = [
  img1, img2, img3, img4, img5,
  img6, img7, img8, img9, img10
];

const facts = [
  "King Ruganzu Ndori expanded Rwanda.",
  "King Mutara III promoted unity.",
  "King Kigeli IV strengthened the kingdom.",
  "Traditional dances are part of Rwandan culture.",
  "Imigongo art is unique to Rwanda.",
  "Intore dancers represent bravery.",
  "Royal drums symbolize power.",
  "Rwandan heritage preserves history.",
  "Cultural values teach unity.",
  "Rwanda has a rich historical legacy."
];

const tiles = ref([]);
const emptyIndex = ref(8);
const currentLevel = ref(0);
const message = ref("");

function startGame() {
  message.value = "";
  tiles.value = [1,2,3,4,5,6,7,8,0];
  shuffle(tiles.value);
  emptyIndex.value = tiles.value.indexOf(0);
}

function shuffle(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
}

function getTileStyle(tile) {
  if (tile === 0) return {};

  const x = (tile - 1) % 3;
  const y = Math.floor((tile - 1) / 3);

  return {
    backgroundImage: `url(${images[currentLevel.value]})`,
    backgroundPosition: `-${x * 100}px -${y * 100}px`
  };
}

function moveTile(index) {
  if (isAdjacent(index, emptyIndex.value)) {
    [tiles.value[index], tiles.value[emptyIndex.value]] =
      [tiles.value[emptyIndex.value], tiles.value[index]];

    emptyIndex.value = index;
    checkWin();
  }
}

function isAdjacent(i1, i2) {
  const r1 = Math.floor(i1 / 3);
  const c1 = i1 % 3;
  const r2 = Math.floor(i2 / 3);
  const c2 = i2 % 3;

  return (
    (r1 === r2 && Math.abs(c1 - c2) === 1) ||
    (c1 === c2 && Math.abs(r1 - r2) === 1)
  );
}

function checkWin() {
  for (let i = 0; i < 8; i++) {
    if (tiles.value[i] !== i + 1) return;
  }

  message.value = "🎉 You Win! " + facts[currentLevel.value];

  setTimeout(() => {
    currentLevel.value++;

    if (currentLevel.value >= images.length) {
      message.value = "🏆 Congratulations! You completed all levels!";
      return;
    }

    startGame();
  }, 2000);
}

onMounted(startGame);
</script>

<style scoped>
.game-container {
  display: flex;
}

/* Sidebar */
.sidebar {
  width: 160px;
  padding: 10px;
}

.preview {
  margin-bottom: 12px;
  text-align: center;
  opacity: 0.6;
  transition: 0.3s;
}

.preview.active {
  opacity: 1;
  border: 3px solid green;
  transform: scale(1.05);
}

.preview img {
  width: 100%;
  height: 90px;
  object-fit: cover;
  border-radius: 6px;
}

/* Game */
.game {
  flex: 1;
  text-align: center;
}

.puzzle {
  width: 300px;
  height: 300px;
  margin: 20px auto;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 2px;
}

.tile {
  width: 100px;
  height: 100px;
  background-size: 300px 300px;
  border: 1px solid #ccc;
  position: relative;
  cursor: pointer;
}

.tile span {
  position: absolute;
  bottom: 5px;
  right: 8px;
  background: rgba(0,0,0,0.6);
  color: white;
  padding: 2px 6px;
  font-size: 14px;
  border-radius: 4px;
}

.empty {
  background: #ddd;
}

.message {
  margin-top: 10px;
}

button {
  padding: 10px 20px;
}
</style>