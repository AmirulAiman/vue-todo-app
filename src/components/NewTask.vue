<script setup>
import {ref} from 'vue'

const task = ref('')
const status = ref('new')
const description = ref('')

const addTodo = () => {
    if(task.value.trim() === '' || status.value.trim() == ''){
        return
    }
    console.log("addTodo")
}
</script>

<template>
    <form class="block mb-6" @submit.prevent="addTodo">
        <div class="flex justify-start mb-6 px-5 py-2">
            <input 
                type="text" 
                name="task" 
                v-model="task" 
                placeholder="Task title..."
                class="w-3/5 px-5 py-2 mx-3 rounded-md"
            >
            <select 
                name="status" 
                id="status" 
                v-model="status"
                class="w-2/5 px-5 py-2 mx-3 rounded-md"
            >
                <option value="">--Select Status--</option>
                <option value="new">New</option>
                <option value="in-progress">In Progress</option>
                <option value="done">Done</option>
            </select>
        </div>
        <div class="w-full px-5 py-2 mb-6">
            <textarea 
                name="description" 
                id="description" 
                cols="10" 
                rows="2" 
                placeholder="Task details..."
                v-model="description"
                class="w-3/5 mx-auto px-5 py-2 rounded-md"
            ></textarea>
        </div>
        <button 
            class="w-3/5 rounded-md px-5 py-2 mx-auto bg-green-600 text-gray-200"
        >Save</button>
    </form>
</template>