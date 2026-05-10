<template>
  <div class="container">
    <h5>Transactions</h5>
    <table class="table">
      <thead>
        <tr>
          <th @click="sortBy('account_name')" style="cursor:pointer">Account {{ indicator('account_name') }}</th>
          <th @click="sortBy('date')" style="cursor:pointer">Date {{ indicator('date') }}</th>
          <th @click="sortBy('payee_name')" style="cursor:pointer">Payee {{ indicator('payee_name') }}</th>
          <th @click="sortBy('category_name')" style="cursor:pointer">Category {{ indicator('category_name') }}</th>
          <th @click="sortBy('memo')" style="cursor:pointer">Memo {{ indicator('memo') }}</th>
          <th @click="sortBy('amount')" style="cursor:pointer">Amount {{ indicator('amount') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="transaction in sortedTransactions" v-bind:key="transaction.id">
          <td>{{transaction.account_name}}</td>
          <td>{{transaction.date}}</td>
          <td>{{transaction.payee_name}}</td>
          <td>{{transaction.category_name}}</td>
          <td>{{transaction.memo}}</td>
          <td>{{convertMilliUnitsToCurrencyAmount(transaction.amount).toFixed(2)}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import {utils} from 'ynab';

export default {
  props: ['transactions'],
  data() {
    return {
      sortKey: 'date',
      sortOrder: 'desc',
    };
  },
  computed: {
    sortedTransactions() {
      const key = this.sortKey;
      const order = this.sortOrder === 'desc' ? -1 : 1;
      return [...this.transactions].sort((a, b) => {
        const aVal = a[key] ?? '';
        const bVal = b[key] ?? '';
        if (aVal < bVal) return -order;
        if (aVal > bVal) return order;
        return 0;
      });
    },
  },
  methods: {
    sortBy(key) {
      if (this.sortKey === key) {
        this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc';
      } else {
        this.sortKey = key;
        this.sortOrder = 'asc';
      }
    },
    indicator(key) {
      if (this.sortKey !== key) return '';
      return this.sortOrder === 'asc' ? '▲' : '▼';
    },
    convertMilliUnitsToCurrencyAmount: utils.convertMilliUnitsToCurrencyAmount,
  },
}
</script>
