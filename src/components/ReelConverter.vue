<script setup>
import { ref, computed, watch } from 'vue';

let symbolsInOrder = ref('Wild,H1,H2,H3,H4,H5,L1,L2,L3,L4,L5,L6,Bonus,Replace');
let reelText = ref(`Bonus	Bonus	Bonus	Bonus	Bonus
L3	L3	L2	L4	L5
H2	L3	H2	H2	H2`);
let reelResult = ref('');
let symsArrStr = ref('');

function process() {
  let result = '';

  // Clean input
  symbolsInOrder.value = symbolsInOrder.value.trim().toUpperCase();
  reelText.value = reelText.value.trim().toUpperCase();

  // Get symbol indices
  let symsArr = [];
  if (symbolsInOrder.value.length > 0) {
    symsArr = symbolsInOrder.value.split(',').map((n) => n.trim());
    symsArrStr.value = symsArr.map((n, i) => `${i}=${n}`).join(',');
  } else symsArrStr.value = '';

  // Get starting data row and count cols
  let rows = reelText.value.split('\n');
  let cols = 0;
  let rowStart = 0;
  for (let i = 0; i < rows.length; i++) {
    let n = rows[i].trim().split('\t').length;
    if (n > 1) {
      cols = n;
      rowStart = i;
      break;
    }
  }

  // Grab data column by column
  for (let j = 0; j < cols; j++) {
    let colSymbols = [];
    for (let i = rowStart; i < rows.length; i++) {
      let sym = rows[i].split('\t')[j];
      if (sym.length == 0) break;
      let index = symsArr.indexOf(sym);
      if (index > -1) sym = index; // Use symbol index of symbol, else string, if available
      colSymbols.push(sym);
    }
    result += `REEL#${j + 1}[${colSymbols.length}] = ${JSON.stringify(
      colSymbols
    )}\n\n`;
  }

  reelResult.value = result;
}

watch([symbolsInOrder, reelText], (newValue, oldValue) => {
  process();
});

process();
</script>

<template>
  <b>SYMBOLS (optional, in order, comma separated):</b>
  <textarea v-model="symbolsInOrder" style="width: 100%" rows="3" />
  <div
    v-if="symsArrStr.length > 0"
    style="margin-bottom: 8px; word-wrap: break-word"
  >
    {{ symsArrStr }}
  </div>
  <b>INPUT (from Excel tab separated):</b>
  <textarea v-model="reelText" style="width: 100%" rows="20" />
  <b>OUTPUT (JSON array):</b>
  <textarea v-model="reelResult" style="width: 100%" rows="15" />
</template>

<style scoped></style>
