<script setup>
import { ref, computed } from 'vue'

const todos = ref([
    { id: 1, text: 'อ่าน Vue', done: true },
    { id: 2, text: 'ทำ Lab', done: false }
])

const remaining = computed(() =>
    todos.value.filter(todo => !todo.done).length
)
const done = computed(() =>
    todos.value.filter(todo => todo.done).length
)
</script>

<template>
    <p><strong>เหลือ {{ remaining }} งาน</strong></p>
    <p><strong>งานเสร็จแล้ว {{ done }} งาน</strong></p>
</template>
