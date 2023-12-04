<script setup>
    import { ref,computed } from 'vue';

    const props = defineProps(['url','expenseId'])
    const submitFlag = ref(0)
    // const expenseData = ref(null)
    const fetchError = ref(0)
    const fetchErrorMsg = ref('')
    const fetchSuccess = ref(0)
    const fetchSuccessMsg = ref('')
    const inputDate = ref('')
    const inputSum = ref(null)
    const inputComment = ref('')
    
    if(props.expenseId > 0) {
        fetch(`${props.url}/${props.expenseId}`, {
            method: "GET",
            headers: {
                "Accept": 'application/json',
            },
        })
        .then((response) => {
            response.json().then((data) => {
                // expenseData.value = data
                if(data.success) {
                    inputDate.value = data.data.date
                    inputSum.value = data.data.sum
                    inputComment.value = data.data.comment
                }            
            })
        })
        .catch((err) => {
            fetchError.value = 1
            fetchErrorMsg.value = err.message
        })
    }
    else {
        inputDate.value = ''
        inputSum.value = ''
        inputComment.value = ''
    }
    const publishedBooksMessage = computed(() => {
        if (props.expenseId == 0) {
            inputDate.value = ''
            inputSum.value = ''
            inputComment.value = ''
        }
    })
    function onSubmit() {
        submitFlag.value = 1
        fetchError.value = 0
        fetchErrorMsg.value = ''
        fetchSuccess.value = 0
        fetchSuccessMsg.value = ''
        const requestOptions = {
            method: "POST",
            headers: {"Content-Type": 'application/json'},
            body: JSON.stringify({
                date: inputDate.value,
                sum: inputSum.value,
                comment: inputComment.value
            })
        }
        if(props.expenseId > 0) {
            fetch(`${props.url}/${props.expenseId}`,requestOptions)
            .then((response) => {
                response.json().then((data) => {
                    if(data.success) {
                        fetchSuccess.value = 1
                        fetchSuccessMsg.value = 'Expense edited. Return to main page'
                        setTimeout(function() {
                            window.location.hash = '/'
                            submitFlag.value = 0;
                            fetchSuccess.value = 0
                        },2000)                
                    }            
                })
            })
            .catch((err) => {
                fetchError.value = 1
                fetchErrorMsg.value = err.message
                submitFlag.value = 0
            })
        }
        else {
            fetch(`${props.url}`,requestOptions)
            .then((response) => {
                response.json().then((data) => {
                    if(data.success) {
                        fetchSuccess.value = 1
                        fetchSuccessMsg.value = 'Expense added. Return to main page'
                        setTimeout(function() {
                            window.location.hash = '/'
                            submitFlag.value = 0
                            fetchSuccess.value = 0
                        },2000)                
                    }            
                })
            })
            .catch((err) => {
                fetchError.value = 1
                fetchErrorMsg.value = err.message
                submitFlag.value = 0
            })
        }
    }
</script>

<template>
    <p>{{ publishedBooksMessage }}</p>
    <form name="employeForm" @submit.prevent="onSubmit">
        <p v-if="fetchError" style="color:#f00">{{ fetchErrorMsg }}</p>
        <p v-if="fetchSuccess" style="color:#0f0">{{ successHandl }}</p>

        <div class="input-box">
            <label>Date:</label>
            <input v-model="inputDate" />
        </div>
        <div class="input-box">
            <label>Summ:</label>
            <input v-model="inputSum" />
        </div>
        <div class="input-box">
            <label>Comment:</label>
            <input v-model="inputComment" />
        </div>
        <div class="input-box">
            <button :disabled="submitFlag" type="submit">Save</button>
        </div>
    </form>
</template>