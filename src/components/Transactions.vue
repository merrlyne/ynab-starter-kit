<template>
  <div class="container">
    <h5>Transactions</h5>
    <table class="table">
      <thead>
        <tr>
          <th :aria-sort="ariaSort('account_name')">
            <button type="button" class="sort-btn" @click="sortBy('account_name')">Account {{ indicator('account_name') }}</button>
          </th>
          <th :aria-sort="ariaSort('date')">
            <button type="button" class="sort-btn" @click="sortBy('date')">Date {{ indicator('date') }}</button>
          </th>
          <th :aria-sort="ariaSort('payee_name')">
            <button type="button" class="sort-btn" @click="sortBy('payee_name')">Payee {{ indicator('payee_name') }}</button>
          </th>
          <th :aria-sort="ariaSort('category_name')">
            <button type="button" class="sort-btn" @click="sortBy('category_name')">Category {{ indicator('category_name') }}</button>
          </th>
          <th :aria-sort="ariaSort('memo')">
            <button type="button" class="sort-btn" @click="sortBy('memo')">Memo {{ indicator('memo') }}</button>
          </th>
          <th :aria-sort="ariaSort('amount')">
            <button type="button" class="sort-btn" @click="sortBy('amount')">Amount {{ indicator('amount') }}</button>
          </th>
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
      const asc = this.sortOrder === 'asc';
      return [...this.transactions].sort((a, b) => {
        const aVal = a[key] ?? '';
        const bVal = b[key] ?? '';
        if (aVal < bVal) return asc ? -1 : 1;
        if (aVal > bVal) return asc ? 1 : -1;
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
    ariaSort(key) {
      if (this.sortKey !== key) return 'none';
      return this.sortOrder === 'asc' ? 'ascending' : 'descending';
    },
    convertMilliUnitsToCurrencyAmount: utils.convertMilliUnitsToCurrencyAmount,
  },
}
</script>

<style scoped>
.sort-btn {
  background: none;
  border: none;
  padding: 0;
  font: inherit;
  cursor: pointer;
}
</style>
