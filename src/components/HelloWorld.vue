<script setup>
import { ref, computed, watch } from 'vue';

let symbolsInOrder = ref('Wild,H1,H2,H3,H4,H5,L1,L2,L3,L4,L5,L6,Bonus,Replace');
let reelText = ref(`Bonus	Bonus	Bonus	Bonus	Bonus
L3	L3	L2	L4	L5
H2	L3	H2	H2	H2`);
let reelResult = ref('');

function process() {
  let symsArr = [];

  if (symbolsInOrder.value.trim().length > 0) {
    symsArr = symbolsInOrder.value
      .toUpperCase()
      .trim()
      .split(',')
      .map((n) => n.trim());
  }

  let reelsStr = reelText.value.trim().toUpperCase();
  let rows = reelsStr.split('\n');
  let cols = rows[0].trim().split('\t').length;
  let arr = [];
  for (let j = 0; j < cols; j++) {
    let colSymbols = [];
    for (let i = 0; i < rows.length; i++) {
      let vals = rows[i].split('\t');
      let sym = vals[j].toUpperCase();
      if (sym.length == 0) continue;
      let index = symsArr.indexOf(sym);
      if (index > -1) sym = index;
      colSymbols.push(sym);
    }
    arr.push(colSymbols);
  }
  reelResult.value = JSON.stringify(arr);
}

watch([symbolsInOrder, reelText], (newValue, oldValue) => {
  process();
});

process();
</script>

<template>
  <b>SYMBOLS IN ORDER (comma separated):</b>
  <textarea v-model="symbolsInOrder" style="width: 100%" rows="3" />
  <b>INPUT (from Excel tab separated):</b>
  <textarea v-model="reelText" style="width: 100%" rows="20" />
  <b>OUTPUT (JSON array):</b>
  <textarea v-model="reelResult" style="width: 100%" rows="15" />
</template>

<style scoped></style>
