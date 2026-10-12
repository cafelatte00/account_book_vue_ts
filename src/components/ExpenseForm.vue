<script setup lang="ts">
import { computed, reactive, ref } from 'vue'
type Expense = {
  id: number
  date: string
  title: string
  amount: number | ''
  category: string
}
// 支出一覧
const expenses = ref<Expense[]>([])

let idCount = 0

const newExpense = reactive<Expense>({
  id: idCount,
  date: '',
  title: '',
  amount: '',
  category: '',
})
// 支出の合計金額
const totalAmount = computed(() => {
  return expenses.value.reduce((total, expense) => {
    return total + Number(expense.amount)
  }, 0)
})
// 今月の支出の合計金額
const totalThisMonth = computed(() => {
  const now = new Date()
  const year = now.getFullYear()
  const month = String(now.getMonth() + 1).padStart(2, '0')
  const thisMonth = `${year}-${month}`
  return expenses.value.reduce((total, expense) => {
    return thisMonth === expense.date.slice(0, 7) ? total + Number(expense.amount) : total
  }, 0)
})

// 登録
function registerExpense() {
  if (!newExpense.date) {
    alert('日付を入力してください。')
    return
  }
  if (!newExpense.title) {
    alert('内容を入力してください。')
    return
  }
  if (newExpense.amount === '' || newExpense.amount < 0) {
    alert('金額を正しく入力してください。')
    return
  }
  if (!newExpense.category) {
    alert('区分を入力してください。')
    return
  }
  idCount++
  console.log('登録が押されました')
  console.log(newExpense)

  expenses.value.push({ ...newExpense, id: idCount })
  newExpense.date = ''
  newExpense.title = ''
  newExpense.amount = ''
  newExpense.category = ''
}
</script>
<template>
  <h2>支出を登録</h2>
  <form @submit.prevent="registerExpense">
    <div>
      <label for="date">日付</label>
      <input id="date" v-model="newExpense.date" type="date" />
    </div>
    <div>
      <label for="title">内容</label>
      <input id="title" v-model.trim="newExpense.title" type="text" />
    </div>
    <div>
      <label for="amount">金額</label>
      <input id="amount" v-model.number="newExpense.amount" min="0" type="number" />
    </div>
    <div>
      <label for="category">区分</label>
      <select id="category" v-model="newExpense.category">
        <option id="category" value="">区分を選択</option>
        <option value="food">食費</option>
        <option value="houseHold">生活用品</option>
        <option value="clothing">被服</option>
        <option value="kids">子ども</option>
        <option value="entertainment">娯楽</option>
        <option value="education">学習</option>
        <option value="social">交際</option>
        <option value="medical">医療</option>
      </select>
    </div>
    <button type="submit">登録</button>
  </form>
  <hr />
  <h2>支出一覧</h2>
  <table border="1">
    <thead>
      <tr>
        <th>日付</th>
        <th>内容</th>
        <th>金額</th>
        <th>区分</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="expense in expenses" :key="expense.id">
        <td>{{ expense.date }}</td>
        <td>{{ expense.title }}</td>
        <td>{{ expense.amount }}</td>
        <td>{{ expense.category }}</td>
      </tr>
    </tbody>
  </table>
  <h3>今月支出合計：{{ totalThisMonth }}円</h3>
  <h3>支出合計：{{ totalAmount }}円</h3>
</template>
<style scoped>
table {
  border-collapse: collapse;
}
</style>
