<script setup>
import { ref, computed } from 'vue'
import ExpenseForm from './components/ExpenseForm.vue';
import ExpenseTable from './components/ExpenseTable.vue';

const API_URL = "http://127.0.0.1:8000/api/expense";

const editedExpenseId = ref(0);

function expenseEditListener(id) {
  editedExpenseId.value = id;
  window.location.hash = '/edit';
}

const routes = {
  '/': ExpenseTable,
  '/add': ExpenseForm,
  '/edit': ExpenseForm
}
let activePage = '/';
const currentPath = ref(window.location.hash);
window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  const pathIndex = currentPath.value.slice(1) || '/';
  if(pathIndex=='/add') {
    editedExpenseId.value = 0;
  }
  return routes[pathIndex] || EmployeTable
})

</script>

<template>
  <main>    
    <nav>
      <a href="#/">Expenses List</a>
      <a href="#/add">Add Expense</a>
    </nav>
    <component :url="API_URL" :is="currentView" :expenseId="editedExpenseId" @editExpense="expenseEditListener" />
  </main>
</template>