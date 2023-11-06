<script>
import Swal from 'sweetalert2';

export default {
  name: 'App',
  methods: {
    useSwalSuccess(){
    Swal.fire({
        toast: true,
        icon: "success",
        title: 'Deleted',
        animation: false,
        position: "top-end",
        showConfirmButton: false,
        timer: 4000,
      });
    },
    useSwalRemoveCompleted(){
      Swal.fire({
        toast: true,
        icon: "success",
        title: 'All Removed Completed',
        animation: false,
        position: "top-end",
        showConfirmButton: false,
        timer: 4000,
      });
    },
    useSwalUpdate(){
      Swal.fire({
        toast: true,
        icon: "success",
        title: 'Update successful',
        animation: false,
        position: "top-end",
        showConfirmButton: false,
        timer: 4000,
      });
    },
    useSwalCancel(){
    Swal.fire({
        toast: true,
        icon: "error",
        title: 'Update canceled !',
        animation: false,
        position: "top-end",
        showConfirmButton: false,
        timer: 4000,
      });
    }
  },
}
</script>
<script setup>
import { ref, computed, watchEffect } from 'vue'

const AppTitle = 'My to do App'
const STORAGE_KEY = ''

const filters = {
  all: (todos) => todos,
  active: (todos) => todos.filter((todo) => !todo.completed),
  completed: (todos) => todos.filter((todo) => todo.completed)
}

// state
const todos = ref(JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]'))
const visibility = ref('all')
const editedTodo = ref()

// derived state
const filteredTodos = computed(() => filters[visibility.value](todos.value))
const remaining = computed(() => filters.active(todos.value).length)

// handle routing
window.addEventListener('hashchange', onHashChange)
onHashChange()

// persist state
watchEffect(() => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(todos.value))
})

function toggleAll(e) {
  todos.value.forEach((todo) => (todo.completed = e.target.checked))
}

function addTodo(e) {
  const value = e.target.value.trim()
  if (value) {
    todos.value.push({
      id: Date.now(),
      title: value,
      completed: false
    })
    e.target.value = ''
  }
}

function removeTodo(todo) {
  todos.value.splice(todos.value.indexOf(todo), 1)

}

let beforeEditCache = ''
function editTodo(todo) {
  beforeEditCache = todo.title
  editedTodo.value = todo
}

function cancelEdit(todo) {
  editedTodo.value = null
  todo.title = beforeEditCache
}

function doneEdit(todo) {
  if (editedTodo.value) {
    editedTodo.value = null
    todo.title = todo.title.trim()
    if (!todo.title) removeTodo(todo)
  }
}

function removeCompleted() {
  todos.value = filters.active(todos.value)
}

function onHashChange() {
  const route = window.location.hash.replace(/#\/?/, '')
  if (filters[route]) {
    visibility.value = route
  } else {
    window.location.hash = ''
    visibility.value = 'all'
  }
}
</script>

<template>
          <div align="center" class="container-fluid card text-center bg-warning mt-1">
          <div align="center" class="container mt-5 bg-white mb-5" >
            <h2 class="text-center">{{AppTitle}}</h2>
            <div class="d-flex mt-4">
             <div class="form-check" v-show="todos.length">
               <input 
               class="form-check-input visually-hidden" 
               type="checkbox" value="" 
               id="flexCheckDefault"
               :checked="remaining === 0"
               @change="toggleAll"
               >
               <label class="btn btn-warning" style="font-size: 14px; color: #818181;" for="flexCheckDefault">
                 Mark all
               </label>
             </div>
             <div style="width: 80%;">
               <input
                 class="form-control border-warning"
                 autofocus
                 placeholder="What is your mission today?"
                 @keyup.enter="addTodo"
               >
             </div>
               
            </div>
            <div class="container card-text mt-4 d-flex justify-content-between">
                 <h4>Task List</h4>
                 <span class="">
                   <strong>{{ remaining }}</strong>
                   <span>{{ remaining === 1 ? ' item' : ' items' }} left</span>
                 </span>
                 <ul class="filters ">
                  <li class="">
                    <a href="#/all" :class="{ selected: visibility === 'all' }" class="btn btn-outline-warning">All</a>
                  </li>
                  <li class="">
                    <a href="#/active" :class="{ selected: visibility === 'active' }" class="btn btn-outline-warning">Active</a>
                  </li>
                  <li class="">
                    <a href="#/completed" :class="{ selected: visibility === 'completed' }" class="btn btn-outline-warning">Completed</a>
                  </li>
                </ul>
            </div>
             <div class="card card-body mb-2">
               <ul class="todo-list">
                 <li
                   v-for="todo in filteredTodos"
                   class="todo"
                   :key="todo.id"
                   :class="{ completed: todo.completed, editing: todo === editedTodo }"
                 >
                   <div class="d-flex justify-content-between">
                     <input class="toggle" type="checkbox" v-model="todo.completed">
                     <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
                     <button @click="removeTodo(todo), useSwalSuccess()">
                       <span class="btn btn-outline-dark fa fa-trash"></span>
                     </button>
                   </div>
                   <input
                     v-if="todo === editedTodo"
                     class="edit"
                     type="text"
                     v-model="todo.title"
                     @vue:mounted="({ el }) => el.focus()"
                     @blur="doneEdit(todo)"
                     @keyup.enter="doneEdit(todo), useSwalUpdate()"
                     @keyup.escape="cancelEdit(todo), useSwalCancel()"
                   >
                 </li>
               </ul>
              <button class="btn btn-secondary" @click="useSwalRemoveCompleted(), removeCompleted()" v-show="todos.length > remaining">
                Clear completed
              </button>
             </div>
           </div>
        </div>
</template>
