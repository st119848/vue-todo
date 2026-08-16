<script setup>
import { reactive } from 'vue'

const form = reactive({
    text: 'TOdo',
    priority: 'normal',
    dueDate: ''
})

function resetForm() {
    form.text = ''
    form.priority = 'normal'
    form.dueDate = ''
}
</script>
<template>
    <h1>Todo Form</h1>
    <form>
        <div>Text : <input id="tf" v-model="form.text"></div>
        <p>Priority : 
            <select>
                <option value="low">Low</option>
                <option value="normal">Normal</option>
                <option value="high">High</option>
            </select>
        </p>
        <div>
            Due Date : <input type="date">
        </div>
    </form>
</template>