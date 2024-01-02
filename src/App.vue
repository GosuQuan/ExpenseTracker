<template>
  <Header></Header>
  <div class="container">
    <Balance :total="total"></Balance>
    <IncomeExpense :expense="+expense" :income="+income"></IncomeExpense>
    <TransactionList
      :transactions="transactions"
      @transaction-deleted="handleTransactionDeleted"
    ></TransactionList>
    <AddTransaction
      @transaction-submitted="handleTransactionSubmitted"
    ></AddTransaction>
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from "vue";
import Balance from "./components/Balance.vue";
import Header from "./components/Header.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";
import { useToast } from "vue-toastification";

onMounted(() => {
  const saveTransactions = JSON.parse(localStorage.getItem("transactions"));
  if (saveTransactions) {
    transactions.value = saveTransactions;
  }
});
const transactions = ref([
  {
    id: 1,
    text: "Flower",
    amount: +500.99,
  },
  { id: 2, text: "Flower2", amount: -19.99 },
  {
    id: 3,
    text: "Flower2",
    amount: -19.99,
  },
  {
    id: 4,
    text: "Flower2",
    amount: -19.99,
  },
]);
const total = computed(() => {
  // console.log(transactions.value.reduce(
  //   (acc, transaction) =>{ return acc + transaction.amount},
  //   0
  // ))
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});
const expense = computed(() => {
  return transactions.value
    .filter((transaction) => {
      return transaction.amount < 0;
    })
    .reduce((pre, cur) => {
      return pre + cur.amount;
    }, 0)
    .toFixed(2);
});
const income = computed(() => {
  return transactions.value
    .filter((transaction) => {
      return transaction.amount > 0;
    })
    .reduce((pre, cur) => {
      return pre + cur.amount;
    }, 0)
    .toFixed(2);
});
const toast = useToast();
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });
  toast.success("Transaction added!");
  saveTransactionsToLocalStorage();
};
const handleTransactionDeleted = (id) => {
  console.log(id);
  transactions.value = transactions.value.filter((transaction) => {
    return transaction.id !== id;
  });
  toast.success("Transaction deleted!");
  saveTransactionsToLocalStorage();
};
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions",JSON.stringify(transactions.value));
};
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};
</script>
