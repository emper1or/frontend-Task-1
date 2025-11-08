<script setup>
import { ref, onMounted, watch } from 'vue'
import BudgetForm from './BudgetForm.vue'
import BudgetTable from './BudgetTable.vue'


const transactions = ref([])

onMounted(() => {
  const savedTransactions = localStorage.getItem('budgetTransactions')
  if (savedTransactions) {
    transactions.value = JSON.parse(savedTransactions)
  }
})

watch(
  transactions,
  (newTransactions) => {
    localStorage.setItem('budgetTransactions', JSON.stringify(newTransactions))
  },
  { deep: true },
)

const addTransaction = (transaction) => {
  transactions.value.push({
    id: Date.now(),
    ...transaction,
  })
}


const deleteTransaction = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
}
</script>

<template>
  <div class="min-h-screen bg-gray-50 text-gray-900 p-6">
    <h1 class="text-3xl font-bold mb-6 text-center">Калькулятор бюджета</h1>

    <div class="max-w-3xl mx-auto space-y-8">
      <BudgetForm @add-transaction="addTransaction" />
      <BudgetTable :transactions="transactions" @delete-transaction="deleteTransaction" />
    </div>
  </div>
</template>
