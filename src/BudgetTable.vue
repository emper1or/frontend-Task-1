<script setup>
import { ref, computed } from 'vue'
import BudgetSummary from './BudgetSummary.vue'

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
})

const emit = defineEmits(['deleteTransaction'])

const sortField = ref('name')
const sortOrder = ref('asc')

const sortedTransactions = computed(() => {
  return [...props.transactions].sort((a, b) => {
    let modifier = sortOrder.value === 'asc' ? 1 : -1

    switch (sortField.value) {
      case 'name':
        return a.name.localeCompare(b.name) * modifier
      case 'type':
        return a.type.localeCompare(b.type) * modifier
      case 'amount':
        return (a.amount - b.amount) * modifier
      default:
        return 0
    }
  })
})

const handleSort = (field) => {
  if (sortField.value === field) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc'
  } else {
    sortField.value = field
    sortOrder.value = 'asc'
  }
}

const formatAmount = (amount, type) => {
  const prefix = type === 'income' ? '+' : '-'
  return `${prefix}${Math.abs(amount)}`
}

const getAmountClass = (type) => {
  return type === 'income' ? 'text-green-600 font-medium' : 'text-red-600 font-medium'
}
</script>

<template>
  <div class="bg-white shadow rounded-xl p-4">
    <h2 class="text-lg font-semibold mb-3">Операции</h2>

    <table class="w-full text-left border-collapse">
      <thead>
        <tr class="border-b">
          <th @click="handleSort('name')" class="p-2 cursor-pointer hover:bg-gray-100">Название</th>
          <th @click="handleSort('type')" class="p-2 cursor-pointer hover:bg-gray-100">Тип</th>
          <th @click="handleSort('amount')" class="p-2 cursor-pointer hover:bg-gray-100">Сумма</th>
          <th class="p-2">Действия</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="transaction in sortedTransactions" :key="transaction.id" class="border-b">
          <td class="p-2">{{ transaction.name }}</td>
          <td class="p-2" :class="getAmountClass(transaction.type)">
            {{ transaction.type === 'income' ? 'Доход' : 'Расход' }}
          </td>
          <td class="p-2" :class="getAmountClass(transaction.type)">
            {{ formatAmount(transaction.amount, transaction.type) }}
          </td>
          <td
            class="p-2 text-sm text-red-500 cursor-pointer"
            @click="emit('deleteTransaction', transaction.id)"
          >
            Удалить
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <BudgetSummary :transactions="transactions" />
</template>
