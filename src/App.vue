<script setup>
  const title = "Todo App"
  const subTitle = "Hello World"
  const imgUrl = "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcST9UjitrpsLDBn81E7T6MBLb9JSMOr5lFtBKtU6dScSA&s=10"
  import {ref} from 'vue'
  import Todo from './components/Todo.vue'
  const count = ref(0)
  function inc(){
    count.value +=1
  }
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <h1 >
        {{ title }} {{ count }}
    </h1>
    <div >
        {{ subTitle }}
        <div>
          <img width="50" :src="imgUrl">
        </div>
        <button @click="inc">Add</button>
    </div>
  </header>
  
  <main>
    <Todo></Todo>
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
