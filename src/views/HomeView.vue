<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import axios from 'axios'
import Header from '../components/Header.vue';

const todos = ref([])
const form_state = ref('create')
const id = ref('')
const task = ref('')
const status = ref('new')
const description = ref('')

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.created - b.created
}))

const isEdit = computed(() => {
  return form_state.value === 'edit' ?? false;
})

//component mounted
onMounted(() => {
  getTodos()
})

//Add todo to todos array
const addTodo = async () => {
  if (task.value.trim() === '' || status.value.trim() == '') {
    return
  }

  await axios.post('http://localhost:5000/api/Todo',{
    task: task.value.trim(),
    description: description.value.trim(),
  })
  .then(res => {
    todos.value.push({
      id: new Date().getTime(),
      task: task.value.trim(),
      status: 'new',
      description: description.value.trim(),
      created: new Date().getTime()
    })
    cancelEdit()
  })
  .catch(err => {
    console.log("message", err.message)
    console.log("response", err.response)
    console.log("request", err.request)
    alert('Failed to add todo')
  })
}

const updateTodo = async(todo) => {
  await axios.put(`http://localhost:5000/api/Todo/${id.value}`, {
    task: task.value.trim(),
    description: description.value.trim(),
    status: status.value,
  })
  .then(res => {
    todos.value = todos.value.filter((t) => t.id !== id.value)
    todos.value.push({
      task: task.value.trim(),
      status: status.value,
      description: description.value.trim(),
    })
    cancelEdit()
  })
  .catch(err => {
    console.log("message",err.message)
    console.log("response",err.response)
    console.log("request",err.request)
    alert('Failed to update')
  })
}

const removeTodo = async (todo) => {
  await axios.delete(`http://localhost:5000/api/Todo/${todo.id}`)
  .then(res => {
    todos.value = todos.value.filter((t) => t !== todo)
  })
  .catch(err => {
    console.log("message", err.message)
    console.log("response", err.response)
    console.log("request", err.request)
    alert('Failed to delete')
  })
}

const editTodo = (todo) => {
  form_state.value = "edit"
  id.value = todo.id
  task.value = todo.task
  description.value = todo.description
  status.value = todo.status
}

const cancelEdit = () => {
  form_state.value = "create"
  id.value = ''
  task.value = ''
  description.value = ''
  status.value = ''
}

const getTodos = async () => {
  await axios.get('http://localhost:5000/api/Todo', {
    "Access-Control-Allow-Origin": "*"
  }).then(res => todos.value = res.data)
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
            <button v-show="!isEdit" type="submit" class="w-full rounded-md px-5 py-2 mx-3 bg-green-600 text-gray-200 mb-6">Save</button>
            <button v-show="isEdit" type="button" @click="updateTodo()" class="w-full rounded-md px-5 py-2 mx-3 bg-orange-300 text-orange-800 mb-6">Update</button>
            <button v-show="isEdit" type="button" @click="cancelEdit()" class="w-full rounded-md px-5 py-2 mx-3 bg-gray-300 text-gray-800 mb-6">Cancel</button>
          </div>
        </form>
      </div>
      <div class="w-3/5 mx-auto items-center px-5 py-3">
        <h3 class="font-semibold text-2xl mb-6">Task List</h3>
        <div v-for="todo in todos_asc"
          :class="`w-full px-6 py-2 mb-6 mx-auto rounded-lg items-center bg-white rounded-mb ${todo.status === 'done' ? 'bg-green-300 text-green-800' : (todo.status === 'in-progress' ? 'bg-yellow-300 text-yellow-800' : 'bg-gray-300 text-gray-800')}`"
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
