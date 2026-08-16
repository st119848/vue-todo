<script setup>
  import {ref} from 'vue'
  const subTitle = "Hello World"
  const imgUrl = "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcST9UjitrpsLDBn81E7T6MBLb9JSMOr5lFtBKtU6dScSA&s=10"
  const title = ref('Vue.js Course')
  const owner = ref('John')
  const remaining = ref(5)
  const isLocked = ref(true)
  import Hello from './components/Hello.vue'
  import TodoForm from './components/TodoForm.vue'
  import ComputedTodo from './components/ComputedTodo.vue'
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <h1 >
        Title {{ title }} 
        
    </h1>
    <h2>
        Owner  {{ owner }}
    </h2>
    <div >
        Remaining : {{ remaining }}
        <button 
           :disabled="isLocked"
           :aria-disabled="isLocked"
        >Add</button>
    </div>
  </header>

  <main>
    <Hello></Hello>
    <TodoForm></TodoForm>
    <ComputedTodo></ComputedTodo>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
