<template>
<Header />
<div class="container">
  <Balance :total="+total"/>
  <IncomeExpense :income="+income" :expenses="+expenses"/>
  <Transactions :transactions="transactions" @transactionDeleted="handleDelete"/>
  <NewTransaction @transactionSubmitted="handleTransaction"/>
</div>
</template>


<script setup>

  import Header from './components/Header.vue'
  import Balance from './components/Balance.vue';
  import IncomeExpense from './components/IncomeExpense.vue';
  import Transactions from './components/Transactions.vue';
  import NewTransaction from './components/NewTransaction.vue';
  import { useToast } from 'vue-toastification';
  import { ref, computed, onMounted } from 'vue';


  const toast = useToast()

  const transactions = ref([])

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem("transactions"))

    if(savedTransactions){
      transactions.value = savedTransactions
    }
  })


//Get Total Balance
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0)
})

//Get Income
const income = computed(() => {
  return transactions.value
  .filter((transaction) => transaction.amount > 0)
  .reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0).toFixed(2)
})


//Get Expense
const expenses = computed(() => {
  return transactions.value
  .filter((transaction) => transaction.amount < 0)
  .reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0).toFixed(2)
})


//generate Unique Id
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000)
}

//Add transaction
const handleTransaction= (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  })

  saveTransactions()

  toast.success("New item Added")
}


//Delete transaction
const handleDelete = (id) => {
  transactions.value = transactions.value.filter(transaction => transaction.id !== id)

  saveTransactions()

  toast.success("Item deleted")
}


//Save to localStorage
const saveTransactions = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}

</script>