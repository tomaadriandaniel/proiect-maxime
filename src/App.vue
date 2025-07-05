<!-- App.vue -->
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
          <div v-for="(cell, idx) in grid" :key="idx" class="cell" :class="cell.revealed ? 'revealed' : 'hidden'"
            @click="cell.revealed = !cell.revealed">
            {{ cell.revealed ? cell.char : '?' }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<!-- SCRIPT -->
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

  // Extragem intrările mapului într-un array și le sortăm după key (clasa)
  const sortedEntries = [...map.entries()].sort((a, b) => a[0] - b[0]);

  maxime.value = [];
  litere.value = [];

  for (let i = 0; i < sortedEntries.length; i++) {
    const value = sortedEntries[i][1]; // maximul pe clasă
    maxime.value.push(value);
    litere.value.push(String.fromCharCode((value % 26) + 65));
  }

  /* -- construim grila 4×4 -- */
  const needed = 16
  const list = []
  let i = 0
  while (list.length < needed) {
    list.push(litere.value[i])
    i = (i + 1) % litere.value.length
  }
  /* shuffle */
  for (let j = list.length - 1; j > 0; j--) {
    const k = Math.floor(Math.random() * (j + 1))
    const temp = list[j];
    list[j] = list[k];
    list[k] = temp;
  }
  grid.value = list.map(ch => ({ char: ch, revealed: false }))
}
</script>

<!-- STYLE -->
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
}

/* Zonă fixă pentru rezultate + grid */
.display-wrapper {
  /* ← rezervă spațiu, împiedică “săritul” */
  min-height: 400px;
  /* ajustează după preferințe */
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

.cell:hover {
  background: #e4e4e4;
}

.cell.hidden {
  background-color: #ffecec;
  /* roșu deschis */
}

.cell.revealed {
  background-color: #eaffea;
  /* verde deschis */
}
</style>
