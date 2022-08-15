<script setup>
import HelloWorld from './components/HelloWorld.vue'
import { ref, computed } from 'vue'
const moneyForwardCsv = ref([])
const message = ref('')
const checkedItems = ref([])
const showItems = computed(() => {
  const splitItem = checkedItems.value?.map((i) => i?.split('"')?.[1])
  const a = splitItem.map((i) => Number(i))
  const init = 0
  const calculateAmount = a.reduce((prev, current) => prev + current, init)
  return `支払い総額　${Math.abs(calculateAmount)}円`
})

function loadCsvFile(e) {
  // moneyForwardCsv.value = []
  // message.value = ""
  const file = e.target.files[0]

  if (!file.type.match('text/csv')) {
    message.value = 'csvファイルを選択してください'
    return
  }

  const reader = new FileReader()
  reader.readAsText(file)

  reader.onload = () => {
    const lines = reader.result.split('\n')
    lines.shift()
    const linesArr = lines.map((line) => line.split(','))
    const convertObjectLines = linesArr
      .map((l) => {
        return {
          usedate: l[1],
          name: l[2],
          usedAmount: l[3],
          financial: l[4],
          largeCategory: l[5],
          smallCategory: l[6],
        }
      })
      .sort(function (a, b) {
        return a.usedate > b.usedate ? 1 : -1
      })
    moneyForwardCsv.value = convertObjectLines
  }
}
</script>

<template>
  <div>
    <input type="file" @change="loadCsvFile" />
    <p>{{ message }}</p>

    <div>
      {{ showItems }}
    </div>
    <!-- {{ checkedItems.map((i) => Numberi.split('"')[1]) }} -->

    <!-- // .reduce((previousValue, currentValue) => previousValue + currentValue, initialValue) -->

    <table border="1">
      <tr v-for="(worker, index) in moneyForwardCsv" :key="index">
        <td v-if="worker.usedAmount">
          <input type="checkbox" :id="index" :value="worker.usedAmount" v-model="checkedItems" />
        </td>
        <td v-for="(column, index) in worker" :key="index">{{ column }}</td>
      </tr>
    </table>

    <!-- <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a> -->
  </div>
  <!-- <HelloWorld msg="Vite + Vue" /> -->
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
