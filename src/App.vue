<script setup>
import { ref, computed } from 'vue'

const Constants = {
  inc: '',
  usedate: '日付',
  name: '内容',
  usedAmount: '金額(円)',
  financial: '保有金融機関',
  largeCategory: '大項目',
  smallCategory: '中項目',
}

// data
const moneyForwardCsv = ref([])
const message = ref('')
const checkedItems = ref([])

// computed
const showcalculateAmount = computed(() => {
  const splitItems = checkedItems.value?.map((i) => i?.split('"')?.[1])
  const convertNumbers = splitItems.map((i) => Number(i))
  const init = 0
  const calculateAmount = convertNumbers.reduce((prev, current) => prev + current, init)
  return `支払い総額　${Math.abs(calculateAmount)}円`
})

// methods
function loadCsvFile(e) {
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
      {{ showcalculateAmount }}
    </div>
    <table border="2" v-if="moneyForwardCsv.length">
      <th v-for="c in Constants">{{ c }}</th>
      <tr v-for="(worker, index) in moneyForwardCsv" :key="index">
        <td v-if="worker.usedAmount">
          <input type="checkbox" :id="index" :value="worker.usedAmount" v-model="checkedItems" />
        </td>
        <td v-for="(column, index) in worker" :key="index">{{ column }}</td>
      </tr>
    </table>
  </div>
</template>

<style scoped></style>
