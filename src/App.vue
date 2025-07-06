<template>
  <div>
    <!-- 1 ▸ TITLU + EROARE -->
    <h1 class="main-title">Maxime pe clase</h1>
    <div :class="['error-panel', { show: showErrorPanel }]">
      {{ errorMessage }}
    </div>

    <!-- 2 ▸ INPUT + EXPLICAȚIE -->
    <div class="container">
      <p class="info-text">
        Introduceți un șir de numere cuprinse între 100 și 999, separate prin virgulă:
      </p>

      <div class="input-row">
        <input v-model="inputString" @keyup.enter="calculeazaMaxime" placeholder="Ex: 110, 120, 280" />
        <button @click="calculeazaMaxime">Calculează</button>
      </div>

      <!-- 3+4 ▸ ZONA REZERVATĂ (rezultate + matrice) -->
      <div class="display-wrapper">
        <div class="result-panel">
          <p v-if="maxime.length">
            <strong>Maximele din clase:</strong>
            [{{ maxime.join(', ') }}]
          </p>
          <p v-if="litere.length">
            <strong>Modulo:</strong>
            [{{ litere.join(', ') }}]
          </p>
        </div>

        <div v-if="grid.length" class="grid-4x4">
          <div v-for="(cell, idx) in grid" :key="idx" class="cell" :class="{
            hidden: cell.state === 0,
            miss: cell.state === 1,
            revealed: cell.state === 2
          }" @click="handleClick(cell)">
            {{ cell.state === 2 ? cell.char : '?' }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

/* -- state -- */
const inputString = ref('')
const maxime = ref([])
const litere = ref([])
const grid = ref([])
const showErrorPanel = ref(false)
const errorMessage = ref('')

/* -- helper: mesaj eroare -- */
function showError(msg) {
  errorMessage.value = msg
  showErrorPanel.value = true
  setTimeout(() => (showErrorPanel.value = false), 4000)
}

/* -- click pe celulă -- */
function handleClick(cell) {
  if (cell.hasLetter) {
    cell.state = cell.state === 2 ? 0 : 2
  } else {
    cell.state = cell.state === 1 ? 0 : 1
  }
}

/* -- calcule -- */
function calculeazaMaxime() {
  const raw = inputString.value.trim();
  if (raw === '') return showError('Introduceți șirul de numere separate prin virgulă!');

  const pattern = /^\s*(\d+\s*,\s*)*\d+\s*(,)?\s*$/;
  if (!pattern.test(raw)) return showError('Introduceți doar cifre separate prin virgulă!');

  const map = new Map();
  const parti = raw.split(',');

  for (let i = 0; i < parti.length; i++) {
    const element = parti[i].trim();
    if (element === '') continue;

    const n = Number(element);
    if (isNaN(n) || n < 100 || n > 999)
      return showError('Numerele trebuie să fie între 100 și 999!');

    const cls = Math.floor(n / 100) * 100;
    if (!map.has(cls) || n > map.get(cls)) map.set(cls, n);
  }

  const sortedEntries = [...map.entries()].sort((a, b) => a[0] - b[0]);

  maxime.value = [];
  litere.value = [];

  for (let i = 0; i < sortedEntries.length; i++) {
    const value = sortedEntries[i][1];
    maxime.value.push(value);
    litere.value.push(String.fromCharCode((value % 26) + 65));
  }

  /* -- construim grila 4×4 -- */
  const TOTAL = 16;
  grid.value = [];
  for (let i = 0; i < TOTAL; i++) {
    grid.value.push({ char: '', hasLetter: false, state: 0 });
  }

  /* alegem poziții aleatoare DISTINCTE pentru fiecare literă */
  const pozitiiDisponibile = [];
  for (let i = 0; i < TOTAL; i++) pozitiiDisponibile.push(i);

  /* amestecăm pozițiile */
  for (let i = pozitiiDisponibile.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    const temp = pozitiiDisponibile[i];
    pozitiiDisponibile[i] = pozitiiDisponibile[j];
    pozitiiDisponibile[j] = temp;
  }

  /* plasăm literele în poziții random */
  for (let i = 0; i < litere.value.length && i < TOTAL; i++) {
    const idx = pozitiiDisponibile[i];
    grid.value[idx].char = litere.value[i];
    grid.value[idx].hasLetter = true;
  }
}
</script>

<style scoped>
/* Titlu + eroare */
.main-title {
  text-align: center;
  margin-top: 0px;
  font-size: 2rem;
}

.error-panel {
  height: 2rem;
  text-align: center;
  background: #ffdddd;
  border: 1px solid red;
  color: darkred;
  font-weight: bold;
  opacity: 0;
  visibility: hidden;
  transition: opacity .3s;
  line-height: 2rem;
}

.error-panel.show {
  opacity: 1;
  visibility: visible;
}

/* Container + input */
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  font-family: sans-serif;
}

.info-text {
  margin-top: -1rem;
  margin-bottom: 0rem;
  font-size: 1rem;
  color: #333;
}

.input-row {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.input-row input {
  flex: 1;
  padding: 0.5rem;
}

.input-row button {
  padding: 0.5rem 1rem;
  background-color: #d1d1d1;
  /* Albastru pronunțat */
  color: rgb(0, 0, 0);
  /* Text alb */
  border: none;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.1s ease-in-out;
}

.input-row button:hover {
  background-color: #a4b8ce;
  /* Albastru mai închis la hover */
}

/* Efect când este apăsat */
.input-row button:active {
  background-color: #d1d1d1;
  transform: scale(0.97);}


/* Zonă fixă pentru rezultate + grid */
.display-wrapper {
  min-height: 400px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

/* Rezultate */
.result-panel {
  background-color: #f4f4f4;
  border: 1px solid #ccc;
  padding: 0.5rem 0.8rem;
  border-radius: 8px;
  min-height: 40px;
  text-align: left;
  font-weight: bold;
}

.result-panel p {
  margin: 0.3rem 0;
  line-height: 1.3;
}

.result-panel strong {
  font-weight: normal;
}

/* Grid 4×4 */
.grid-4x4 {
  margin-top: 2rem;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.5rem;
}

.cell {
  aspect-ratio: 1/1;
  background: #fafafa;
  border: 1px solid #bbb;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 3rem;
  font-weight: 500;
  cursor: pointer;
  user-select: none;
  transition: background .2s;
}

/* Culoare default: roșu deschis (hidden) */
.cell.hidden {
  background-color: #ee5555;
}

/* Culoare pentru click greșit (miss) */
.cell.miss {
  background-color: #30a9be;
}

/* Culoare pentru litere descoperite (revealed) */
.cell.revealed {
  background-color: #35f535;
}
</style>
