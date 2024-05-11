<script setup>

import { ref, onMounted, onUpdated, computed, watch } from 'vue'

const todos = ref([])

const name = ref('')

const input_content = ref('')

const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

const clearCompleted = () => {
  todos.value = todos.value.filter(todo => !todo.done)
}
        
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

onUpdated(() => {
  console.log("Ada suatu perubahan terjadi")
})

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Hai!, <input class="text" type="text" placeholder="enter name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="e.g. read a book" v-model="input_content" />

        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input type="radio" name="category" value="thisweek" v-model="input_category" />
            <span class="bubble thisweek"></span>
            <div> This week</div>
          </label>
          
          <label>
            <input type="radio" name="category" value="nextweek" v-model="input_category" />
            <span class="bubble nextweek"></span>
            <div>Next Week</div>
          </label>


          <label>
            <input type="radio" name="category" value="nanti" v-model="input_category" />
            <span class="bubble nanti"></span>
            <div>Later</div>
          </label>

        </div>

        <input type="submit" value="Add todo" />

      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">

        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

          <label >
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>


          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>

        </div>

      </div>
    </section>

    <section class="clear">
      <div class="clearbtns">
            <button class="delete" @click="clearCompleted(todo)">Clear Completed</button>
          </div>
    </section>

  </main>
</template>