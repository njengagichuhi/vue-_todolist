<script setup>
import { ref, onMounted, computed, watch } from "vue";
const todo = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);
const todo_asc = computed(() =>
  todo.value.sort((a, b) => {
    return b.createdAT - a.createdAT;
  })
);

const removeTodo=todo=>{
    todo.value = todo.value.filter(t=>t !== todo)
              };          
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }


  todo.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAT: new Date().getTime(),
  });
  input_content.value=''
  input_category.value=null
};
watch(
  todo,
newVal => {
    localStorage.setItem("todo", JSON.stringify(newVal));
  },
  { deep: true }
);
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todo.value=JSON.parse(localStorage.getItem('todo')|| '[]')
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        what's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TO DO</h3>
      <form  @submit.prevent="addTodo">
        <h4>Whats in your to do list?</h4>
        <input
          type="text"
          placeholder="e.g make a video"
          v-model="input_content"
        />

        <h4>Pick a category</h4>
        <div class="options">
          <label >
            <input
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label >
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <div v-for="todo in todo_asc" :key="todo" :class="'todo-item ${todo.done &&  }'" >
          <label >
            <input type="checkbox" v-model="todo.done">
            <span :class="'bubble ${todo.category}'"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
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
