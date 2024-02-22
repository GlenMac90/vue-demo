<template>
    <div>
        <h3>History</h3>
        <ul id='list' class='list'>
            <li 
                v-for="transaction in transactions"
                :key="transaction.id" 
                :class="transaction.amount < 0 ? 'minus' : 'plus'"
            >
                {{transaction.text}} <span>${{transaction.amount}}</span><button @click="handleDelete(transaction.id)" class='delete-btn'>x</button>
            </li>
        </ul>
    </div>
</template>

<script setup>
    const props = defineProps({
        transactions: Array,
        required: true
    })

    const emit = defineEmits(['deleteTransaction'])

    const handleDelete = (id) => {
        emit('deleteTransaction', id)
    }
</script>