<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed( () => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt;
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

watch(todos, newVaL => {
  localStorage.setItem('todos', JSON.stringify(newVaL))
}, {deep: true})

watch(name, (newVaL) => {
  localStorage.setItem('name', newVaL)
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
                Halo, <input type="text" placeholder="Nama Disini"
                v-model="name" />
            </h2>
        </section>

        <section class="create-todo">
            <h3>Membuat sebuah Todo</h3>

            <form @submit.prevent="addTodo">
                <h4>Kegiatan apa yang kamu lakukan Hari ini?</h4>
                <input 
                   type="text" 
                   placeholder="cth belajar" 
                   v-model="input_content" />
        
                <h4>Pilih kategori</h4>

                <div class="options">

                    <label>
                        <input 
                            type="radio"
                            name="category"
                            id="category1"
                            value="business"
                            v-model="input_category" />
                        <span class="bubble business"></span>
                        <div>Kerjaan</div>
                    </label>

                    <label>
                        <input 
                            type="radio"
                            name="category"
                            id="personal1"
                            value="personal"
                            v-model="input_category" />
                        <span class="bubble personal"></span>
                        <div>Pribadi</div>
                    </label>

                </div>

                <input type="submit" value="Tambahkan Kegiatan" />
            </form>
        </section>

        <section class="todo-list">
            <h3>Kegiatan Harian</h3>
            <div class="list">
                <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble &{todo.category}`"></span>
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
    </main>

</template>

<style scoped>



</style>
