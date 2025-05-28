<template>
  <section class="flex items-center justify-between mb-10">
    <h1 class="text-xl font-extrabold">Summary</h1>
    <div>
      <USelectMenu v-model="selectedView" :options="transactionViewOptions" />
    </div>
  </section>

  <section
    class="grid gird-cols-1 sm:grid-col-2 lg:grid-cols-4 sm:gap-16 mb-10"
  >
    <Trend
      color="green"
      title="Income"
      :amount="4000"
      :last-amount="3000"
      :loading="pending"
    />
    <Trend
      color="red"
      title="Expense"
      :amount="500"
      :last-amount="2000"
      :loading="pending"
    />
    <Trend
      color="green"
      title="Investments"
      :amount="300"
      :last-amount="100"
      :loading="pending"
    />
    <Trend
      color="red"
      title="Savings"
      :amount="0"
      :last-amount="1600"
      :loading="pending"
    />
  </section>

  <section class="flex justify-between mb-10">
    <div>
      <h2 class="text-2xl font-extrabold">Transactions</h2>
      <div class="text-gray-500 dark:text-gray-400">
        You have {{ incomeCount }} incomes and {{ expenseCount }} expenses this
        period
      </div>
    </div>
    <TransactionModal v-model="isOpen" @saved="refresh()" />
  </section>

  <section v-if="!pending">
    <div
      v-for="(transactionOnDay, date) in byDate"
      :key="transactionOnDay.date"
      class="mb-10"
    >
      <DailyTransactionSummary :date="date" :transactions="transactionOnDay" />
      <Transaction
        v-for="transaction in transactionOnDay"
        :key="transaction.id"
        :transaction="transaction"
        @deleted="refresh()"
      />
    </div>
  </section>
  <section v-else>
    <USkeleton class="h-8 w-full mb-2" v-for="i in 4" :key="i" />
  </section>
</template>

<script setup>
import { transactionViewOptions } from "~/constants.js";

const selectedView = ref(transactionViewOptions[1]);
const isOpen = ref(false);

const { current, previous } = useSelectedTimePeriod(selectedView);

const {
  pending,
  refresh,
  transactions: {
    incomeCount,
    expenseCount,
    incomeTotal,
    expenseTotal,
    grouped: { byDate },
  },
} = useFetchTransactions(current);

const {
  transactions: {
    incomeTotal: prevIncomeTotal,
    expenseTotal: prevExpenseTotal,
  },
} = useFetchTransactions(previous);

//await refresh(); // alrtrady called with {immediate:true} in composable
</script>
