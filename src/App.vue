<template>
  <Header />
  <div class='container'>
    <Balance :total="+total" />
    <IncomeExpenses :expenses="+expenses" :income="+income" />
    <TransactionList @deleteTransaction="handleTransactionDeleted" :transactions="transactions" />
    <AddTransaction @addTransaction="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
  import {useToast} from 'vue-toastification' 

  import Header from './components/Header.vue'
  import Balance from './components/Balance.vue'
  import IncomeExpenses from './components/IncomeExpenses.vue'
  import TransactionList from './components/TransactionList.vue'
  import AddTransaction from './components/AddTransaction.vue'

  import { ref, computed, onMounted } from 'vue'

  const toast = useToast()

  const transactions = ref([])

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

    if (savedTransactions) {
      transactions.value = savedTransactions
    }
  })

  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0)
  })

  const expenses = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    if (transaction.amount < 0) {
      return acc + transaction.amount;
    }
    return acc;
  }, 0).toFixed(2);
});

  const income = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
    if (transaction.amount > 0) {
      return acc + transaction.amount;
    }
    return acc;
  }, 0).toFixed(2);
  })

  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount
    })

    toast.success('Transaction added')

    saveToLocalStorage()
  }

  const generateUniqueId = () => {
    return Math.floor(Math.random() * 100000000)
  }

  const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter(transaction => transaction.id !== id)

    toast.success('Transaction deleted')

    saveToLocalStorage()
  }

  const saveToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }
</script>