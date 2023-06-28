<script setup>
import { ref, onMounted, computed, watch } from "vue";

const tasks = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const tasks_asc = computed(() =>
  tasks.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  tasks.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
  tasks.value = tasks.value.filter((t) => t !== todo);
};

const editTodo = (todo) => {
  tasks.value = tasks.value.filter((t) => t === todo);
};

watch(
  tasks,
  (newVal) => {
    localStorage.setItem("tasks", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  tasks.value = JSON.parse(localStorage.getItem("tasks") || []);
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hi <input type="text" placeholder="Name here" v-model="name" />
        {{ new Date() }}
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TASK</h3>

      <form @submit.prevent="addTodo">
        <input
          type="text"
          name="content"
          id="content"
          planceholder="e.g. make something..."
          v-model="input_content"
        />

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

          <input type="submit" value="Add Task" />
        </div>
      </form>
    </section>

    <section class="todo-list">
      <h3>TASK LIST</h3>
      <div class="list">
        <div
          v-for="todo in tasks_asc"
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
            <button class="delete" @click="removeTodo(todo)">Delete</button>
            <span
              ><button class="edit" @click="editTodo(todo)">Edit</button></span
            >
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
