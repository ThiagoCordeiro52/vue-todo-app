<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const inputContent = ref("");
const inputCategory = ref("");

const todosDescendant = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    createdAt: new Date().getTime(),
    done: false,
  });

  inputContent.value = "";
  inputCategory.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((element) => element !== todo);
};

watch(
  todos,
  (newValue) => {
    localStorage.setItem("todos", JSON.stringify(newValue));
  },
  { deep: true }
);

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        E aí,
        <input type="text" placeholder="digita aqui seu nome:" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Criar uma tarefa</h3>
      <form @submit.prevent="addTodo">
        <h4>O que está na sua lista de tarefas?</h4>
        <input
          type="text"
          placeholder="ex.: gravar um vídeo"
          v-model="inputContent"
        />
        <h4>Selecione uma categoria</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory"
            />
            <span class="bubble business"></span>
            <div>Negócios</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory"
            />
            <span class="bubble personal"></span>
            <div>Pessoais</div>
          </label>
        </div>

        <input type="submit" value="Adicionar tarefa" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Lista de tarefas</h3>
      <div class="list">
        <div
          v-for="todo in todosDescendant"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Deletar</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
