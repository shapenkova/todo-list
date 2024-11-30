<template>
  <main class="app">
    <section class="greeting">
      <h2 class="greeting__title title">
        Ваш список заданий, <input type="text" placeholder="Ваше имя" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3 class="create-todo__title">СОЗДАЙ ЗАДАЧУ</h3>

      <form @submit.prevent="addTodo" class="create-todo__form form">
        <input 
          class="form__input"
          type="text" 
          placeholder="Что нужно сделать..." 
          v-model="inputContent"
        >

        <h4 class="form__title">Выбери категорию</h4>

        <div class="form__options options">

          <label class="options__label">
            <input 
              class="options__input"
              type="radio"
              name="category"
              value="work"
              v-model="inputCategory"
            >
            <span class="bubble work"></span>
            <div class="options__description">Работа</div>
          </label>

          <label class="options__label">
            <input 
              class="options__input"
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory"
            >
            <span class="bubble personal"></span>
            <div class="options__description">Личные</div>
          </label>

        </div>

        <input type="submit" value="Добавить задачу" class="form__btn">
      </form>
    </section>

    <section class="todo-list">
      <h3 class="todo-list__title">Список задач</h3>
      <div class="todo-list__box">

        <div 
          v-for="todo in todosAsc" 
          :key="todo.id" 
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label class="todo-list__label">
            <input type="checkbox"  v-model="todo.done" class="todo-list__input">
            <span :class="`bubble ${todo.category}`" class="todo-list__bubble"></span>
          </label>

          <div class="todo-list__content todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="todo-list__actions actions">
            <button class="todo-list__btn delete" @click="removeTodo(todo)">Удалить</button>
          </div>
        </div>

      </div>
    </section>

  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const inputContent = ref('')
const inputCategory = ref(null)

const todosAsc = computed(() => todos.value.sort((a,b) => {
  return b.createAt - a.createAt
}))

const addTodo = () => {
  if(inputContent.value.trim() === '' || inputCategory.value === null){
    return
  }
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createAt: new Date().getTime()
  })
  inputContent.value = ''
  inputCategory.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') || [])
})
</script>