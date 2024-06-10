<script setup>
import { computed, ref, onMounted, watch } from 'vue'
import { setTheme } from './utils/theme';

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => {
  return [...todos.value].sort((a, b) => b.created_at - a.created_at)
})

const addToDo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    created_at: new Date().getTime()
  })
  // Clear inputs after adding
  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

const changeTheme = (event) => {
  const selectedTheme = event.target.value;
  setTheme(selectedTheme);
}

watch(todos, (newvalue) => {
  localStorage.setItem('todos', JSON.stringify(newvalue))
}, { deep: true })

watch(name, (newvalue) => {
  localStorage.setItem('name', newvalue)
})

onMounted(() => {
  const storedName = localStorage.getItem('name')
  name.value = storedName || ''

  const storedTodos = localStorage.getItem('todos')
  try {
    todos.value = storedTodos ? JSON.parse(storedTodos) : []
    if (!Array.isArray(todos.value)) {
      todos.value = []
    }
  } catch (e) {
    todos.value = []
  }
})
</script>

<template>
  <main>
    <section class="flex justify-between  m-5">
      <h2 class="text-nuetral-content text-2xl font-extrabold">
        What's Up,
        <input class="input input-ghost input-lg p-0 text-2xl" type="text" placeholder="Name here" v-model="name">
      </h2>

      <div class="flex items-center space-x-4">
            <label for="theme-select" class="hidden">Choose Theme:</label>
            <select id="theme-select" class="select select-bordered" @change="changeTheme">
                <option value="light">Light</option>
                <option value="dark">Dark</option>
                <option value="cupcake">Cupcake</option>
                <option value="bumblebee">Bumblebee</option>
                <option value="emerald">Emerald</option>
                <option value="corporate">Corporate</option>
                <option value="cyberpunk">Cyberpunk</option>
                <option value="synthwave">ynthwave</option>
                <option value="retro">retro</option>
                <option value="lofi">lofi</option>
                <option value="wireframe">wireframe</option>
                <option value="luxury">luxury</option>
                <option value="business">business</option>
                <option value="lemonade">lemonade</option>
                <option value="coffee">coffee</option>
                <option value="dim">Dim</option>
                <option value="Nord">Nord</option>
                <option value="sunset">Sunset</option>
                <option value="forest">Forest</option>
                <!-- Add more options if you add more themes -->
            </select>
        </div>
    </section>

    <section class="grid grid-flow-row gap-4 m-5">
      <h3 class="text-lg text-nuetral-content">Create a To-Do</h3>
      <form @submit.prevent="addToDo">
        <h4 class="text-lg text-nuetral-content">What's On Your To Do List?</h4>
        <input type="text" placeholder="e.g. Study Maybe?!" v-model="input_content"
          class="input input-md w-full input-bordered input-primary my-3">
        <div class="flex justify-center mb-2">
          <h4 class="text-lg text-nuetral-content">Pick A Category:</h4>
        </div>
        <div class="flex space-x-4 justify-center">
          <label class="cursor-pointer">
            <input type="radio" name="category" class="peer sr-only" value="business" v-model="input_category">
            <div
              class="w-64 py-2 text-center border rounded-lg peer-checked:border-secondary peer-checked:bg-blue-50 peer-checked:text-secondary text-nuetral-content">
              Business
            </div>
          </label>
          <label class="cursor-pointer">
            <input type="radio" name="category" class="peer sr-only" value="personal" v-model="input_category">
            <div
              class="w-64 py-2 text-center border rounded-lg peer-checked:border-primary peer-checked:bg-pink-50 peer-checked:text-primary text-nuetral-content">
              Personal
            </div>
          </label>
        </div>
        <div class="flex justify-center">
          <input type="submit" value="Add To-do" class="my-5 w-72 h-12 bg-info-content text-primary rounded-2xl cursor-pointer">
        </div>
      </form>
    </section>

    <section>
      <h3 class="text-xl text-nuetral-content m-5 font-bold">To Do List</h3>
      <div class="max-w-xl mx-auto p-4">
        <div class="flex flex-col space-y-2">
          <div v-for="todo in todos_asc" :key="todo.created_at"
            :class="['todo-item flex items-center space-x-4 p-2 border rounded shadow-md', { done: todo.done }]">
            <label class="flex items-center space-x-2 cursor-pointer">
              <input type="checkbox" v-model="todo.done" class="hidden">
              <span
                :class="`bubble ${todo.category} flex items-center justify-center w-5 h-5 rounded-full border-2 box-shadow transition`"></span>
            </label>

            <div class="todo-content flex-1">
              <input type="text" v-model="todo.content" class="w-full bg-transparent text-xl outline-none">
            </div>

            <div class="actions">
              <button @click="removeTodo(todo)"
                class="bg-red-500 text-white px-3 py-1 rounded transition hover:opacity-75">
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style>
.done .todo-content input {
  text-decoration: line-through;
  color: var(--grey);
}
</style>
