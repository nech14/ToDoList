
<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)
const input_importance = ref('Usually')

const colorClass = ['bg-red-500', 'bg-blue-500']

const todos_asc = computed(() => {
  const todosCopy = [...todos.value]; // Создаем копию массива
  return todosCopy.sort((a, b) => b.createdAt - a.createdAt);
});

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null){
    return 
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    importance: input_importance.value,
    done: false,
    createAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

watch(name, newVal =>{
  localStorage.setItem('name', newVal)
})

onMounted(() =>{
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos'))  || []
})

</script>

<template>

  <!-- Ваш HTML-код -->
<main class="flex p-8">
  <div class="w-screen max-w-md mx-auto bg-white rounded-md shadow-md overflow-hidden">
    <section class="w-full p-6">
      <form @submit.prevent="addTodo">

        <h4 class="text-lg font-semibold mb-2">What's on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. make a eat"
          v-model="input_content"
          class="w-full border border-gray-300 rounded-md p-2 mb-4 focus:outline-none focus:border-blue-500"
        />

        <h4 class="text-lg font-semibold mb-2">Pick a category</h4>
        <div class="mb-4 flex">
          <label class="inline-flex items-center mr-4 align-middle">
            <input 
              type="radio"
              name="category"
              value="0"
              v-model="input_category"
              class="form-radio hidden"
            />               
            <span :class="`w-6 h-6 flex items-center justify-center rounded-full border-4 border-red-500 bg-white`">
              <span v-if="input_category === '0'" :class="`w-2 h-2 rounded-full ${colorClass[0]}`"></span>
            </span>     
            <span class="ml-2">Business</span>
          </label>

          <label class="inline-flex items-center align-middle ml-10">
            <input 
              type="radio"
              name="category"
              value="1"
              v-model="input_category"
              class="form-radio hidden"
            />            
            <span :class="`w-6 h-6 flex items-center justify-center rounded-full border-4 border-blue-500 bg-white`">
              <span v-if="input_category === '1'" :class="`w-2 h-2 rounded-full ${colorClass[1]}`"></span>
            </span>  
            <span class="ml-2">Personal</span>
          </label>
        </div>


        <div class="my-8">
          <label for="importance" class="block text-sm font-medium text-gray-700">Select importance:</label>
          <div class="mt-1 relative rounded-md shadow-sm">
            <select v-model="input_importance" class="block appearance-none w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:border-blue-500">
              <option value="Usually">Usually</option>
              <option value="Important">Important</option>
              <option value="Very important">Very important</option>
              <option value="Highest priority">Highest priority</option>
            </select>
          </div>
        </div>

        <input type="submit" value="Add todo" class="float-right bg-blue-600 text-white py-2 px-4 rounded-md hover:bg-blue-300 cursor-pointer"/>
      </form>
    </section>

    <section class="p-6">
      <h3 class="text-xl font-semibold mb-4">TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :key="todo.id" :class="`todo-item ${todo.done && 'done'}`">

          <label class="flex items-center align-middle">
            <input type="checkbox" v-model="todo.done" class="hidden"/>
            <span :class="`w-6 h-6 flex items-center justify-center rounded-full border-1 border-white ${todo.done ? 'bg-green-500' : colorClass[todo.category]}`">
              <span v-if="!todo.done" class="w-4 h-4 rounded-full bg-white"></span>
            </span>
            <div class="max-w-[300px] p-2">
              <span :class="`ml-2 text-gray-700 ${todo.done ? 'line-through text-gray-200' : ''} select-none max-w-[300px]`">{{ todo.content }}</span>
              <span :class="`select-none max-w-[300px] block truncate`">
                <span :class="`text-gray-700 ${todo.done ? 'line-through text-gray-200' : ''}`">Importance: </span>
                <span :class="`${todo.done ? 'line-through text-gray-200' : 'text-red-500'}`">{{ todo.importance }}</span>
              </span>
            </div>
            <div class="actions ml-auto p-2 pb-4">
              <button class=" text-red-500 hover:text-red-700" @click="removeTodo(todo)">Delete</button>
            </div>
          </label>

          
        </div>
      </div>
    </section>
  </div>
</main>


</template>