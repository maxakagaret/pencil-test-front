<script setup>
    import { ref } from 'vue'
    const props = defineProps(['url','expenseId'])
    const expenseData = ref(null)
    const fetchError = ref(0)
    const fetchErrorMsg = ref('')
    fetch(props.url, {
        method: "GET",
        headers: {
            "Accept": 'application/json',
        },
    })
    .then((response) => {
        response.json().then((data) => {
            expenseData.value = data
        })
    })
    .catch((err) => {
        fetchError.value = 1
        fetchErrorMsg.value = err.message
    })
</script>
<template>
    <p v-if="fetchError">Fetch Error: {{ fetchErrorMsg }}</p>
    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Date</th>
                <th>Summ</th>
                <th>Comment</th>
                <th>Edit</th>
            </tr>
        </thead>
        <tbody v-if="expenseData">
            <tr v-for="{ id, date, sum, comment } in expenseData.data" >
                <td>{{ id }}</td>
                <td>{{ date }}</td>
                <td>{{ sum }}</td>
                <td>{{ comment }}</td>
                <td><button type="button" :value="id" @click="$emit('editExpense',id)">Edit</button></td>
            </tr>
        </tbody>
        <tbody v-else>
            <tr><td colspan="6">No employes</td></tr> 
        </tbody>
    </table>
</template>