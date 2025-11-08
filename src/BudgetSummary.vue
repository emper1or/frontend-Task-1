<script setup>
import { computed } from 'vue'

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
})

const totalIncome = computed(() => {
  return props.transactions
    .filter((t) => t.type === 'income')
    .reduce((sum, transaction) => sum + transaction.amount, 0)
})

const totalExpenses = computed(() => {
  return props.transactions
    .filter((t) => t.type === 'expense')
    .reduce((sum, transaction) => sum + transaction.amount, 0)
})

const balance = computed(() => {
  return totalIncome.value - totalExpenses.value
})

const isNegativeBalance = computed(() => {
  return balance.value < 0
})
</script>

<template>
  <div class="bg-white shadow rounded-xl p-4 text-center">
    <h2 class="text-lg font-semibold mb-3">Сводка</h2>

    <div class="grid grid-cols-3 gap-4">
      <div>
        <p class="text-gray-500">Доходы</p>
        <p class="text-green-600 text-xl font-semibold">+{{ totalIncome }}</p>
      </div>
      <div>
        <p class="text-gray-500">Расходы</p>
        <p class="text-red-600 text-xl font-semibold">-{{ totalExpenses }}</p>
      </div>
      <div>
        <p class="text-gray-500">Баланс</p>
        <p
          :class="[
            'text-xl font-semibold',
            { 'text-red-600': isNegativeBalance, 'text-green-600': !isNegativeBalance },
          ]"
        >
          {{ balance >= 0 ? '+' : '' }}{{ balance }}
        </p>
      </div>
    </div>

    <p v-if="isNegativeBalance" class="mt-4 text-red-500 font-medium">
      ⚠️ Ваш баланс отрицательный!
    </p>
  </div>
</template>
