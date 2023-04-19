<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import Header from '../components/Header.vue';

const todos = ref([])
const form_state = ref('create')
const id = ref('')
const task = ref('')
const status = ref('new')
const description = ref('')

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

const isEdit = computed(() => {
  return form_state.value === 'edit' ?? false;
})

//Start of cycle
onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

//Add todo to todos array
const addTodo = () => {
  if (task.value.trim() === '' || status.value.trim() == '') {
    return
  }

  if(form_state.value == 'create') {
    todos.value.push({
      id: new Date().getTime(),
      task: task.value.trim(),
      status: 'new',
      description: description.value.trim(),
      createdAt: new Date().getTime()
    })
  } else {
    todos.value = todos.value.filter((t) => t.id !== id.value)
    todos.value.push({
      id: id.value,
      task: task.value.trim(),
      status: status.value,
      description: description.value.trim(),
      createdAt: new Date().getTime()
    })
  }
  id.value = ''
  task.value = ''
  status.value = ''
  description.value = ''
  form_state.value = "create"
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

const editTodo = (todo) => {
  form_state.value = "edit"
  id.value = todo.id
  task.value = todo.task
  description.value = todo.description
  status.value = todo.status
}

//Check for any change to todos
//deep: check every content change in todos
watch(todos, (newTodos) => {
  localStorage.setItem('todos', JSON.stringify(newTodos));
}, { deep: true })
</script>

<template>
  <div class="h-full">
    <div class="rounded-sm px-5 py-2 mx-auto">
      <Header title="Todos" />
    </div>
    <div class="container shadow-md">
      <div class="w-3/5 mx-auto">
        <form class="mb-6 flex-col items-center" @submit.prevent="addTodo">
          <input type="hidden" name="id" v-model="id">
          <input 
            type="text" 
            name="task" 
            v-model="task" 
            placeholder="Task title..."
            class="w-full px-5 py-2 mx-3 rounded-md mb-3">
          <textarea 
            name="description" 
            id="description" 
            cols="10" 
            rows="2" 
            placeholder="Task details..."
            v-model="description" 
            class="w-full px-5 py-2 mx-3 mb-6 rounded-md"
          ></textarea>
          <div v-show="isEdit" class="w-3/5 mx-auto mb-6">
            <select name="status" id="status" v-model="status" class="w-full px-5 py-2 mx-3 rounded-md">
              <option value="new">New</option>
              <option value="in-progress">In Progress</option>
              <option value="done">Done</option>
            </select>
          </div>
          <div class="w-3/5 mx-auto">
            <button class="w-full rounded-md px-5 py-2 mx-3 bg-green-600 text-gray-200">{{ IsEdit === true ? 'Update' : 'Save' }}</button>
          </div>
        </form>
      </div>
      <div class="w-3/5 mx-auto items-center px-5 py-3">
        <h3 class="font-semibold text-2xl mb-6">Task List</h3>
        <div v-for="todo in todos_asc"
          :class="`w-full px-6 py-2 mb-6 mx-auto rounded-lg items-center bg-white rounded-mb ${todo.status === 'done' ? 'bg-green-200 text-green-800' : (todo.status === 'in-progress' ? 'bg-yellow-200 text-yellow-800' : 'bg-gray-200 text-gray-800')}`"
          @dblclick="removeTodo(todo)"
        >
        <div class="flex items-center">
          <div class="w-full flex-col px-5 py-2 mx-auto">
            <h1 class="text-md font-bold">{{ todo.task }}</h1>
            <p>{{ todo.description }}</p>
          </div>
          <div class="flex-col sm:flex sm:items-center sm:mx-6 sm:justify-between space-y-1">
              <button class="bg-orange-200 text-orange-800 px-3 py-1 rounded-lg mx-6 hover:bg-orange-800 hover:text-orange-200 transition-colors duration-200" @click="editTodo(todo)">
                <i class="fa-solid fa-pen-to-square"></i>
              </button>
              <button class="bg-red-200 text-red-800 px-3 py-1 rounded-lg mx-6 hover:bg-red-800 hover:text-red-200 transition-colors duration-200" @click="removeTodo(todo)">
                <i class="fa-solid fa-trash"></i>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
