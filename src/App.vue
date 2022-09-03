<script setup>
import { ref, onMounted, computed, watch } from "vue";
const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
  input_category.value = "";
};
const deleteTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
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
  <main>
    <div class="greeting">
      <h2>
        What's up,
        <input
          class="input-name"
          placeholder="Name here"
          type="text"
          v-model="name"
        />
      </h2>
    </div>
    <div class="create-todo">
      <h3>Create a todo</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          placeholder="your todo"
          v-model="input_content"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label for="business">
            <input
              type="radio"
              name="category"
              id="business"
              value="business"
              v-model="input_category"
            />
            <span>Business</span>
          </label>
          <label for="home">
            <input
              type="radio"
              name="category"
              id="home"
              value="home"
              v-model="input_category"
            />
            <span>Home</span>
          </label>
        </div>
        <button
          type="submit"
          class="btn btn--add"
        >
          Add todo
        </button>
      </form>
    </div>
    <div class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :key="todo.createdAt"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label for="">
            <input
              type="checkbox"
              :id="todo.category"
              v-model="todo.done"
            />
          </label>
          <div class="todo-content">
            <input
              type="text"
              v-model="todo.content"
            />
          </div>
          <div class="actions">
            <button
              type="button"
              class="btn btn-delete"
              @click="deleteTodo(todo)"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
main {
  display: grid;
  gap: 1rem;
}
h3,
h4 {
  margin-top: 0.875rem;
  margin-bottom: 0.875rem;
}
.greeting h2 {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
}
.greeting h2 input {
  background: transparent;
  margin-left: 0.5rem;
  flex: 1 1 0%;
  overflow: hidden;
  text-overflow: ellipsis;
}
.btn {
  box-shadow: 0 0 5px 0 #9c663a;
  background: linear-gradient(to bottom, #b35960 5%, #99406c 100%);
  border-radius: 0.5rem;
  display: inline-block;
  cursor: pointer;
  color: #ffffff;
  font-size: 0.75rem;
  font-weight: bold;
  padding: 0.3rem 0.5rem;
  text-decoration: none;
  text-shadow: 0px 1px 0px #3d768a;
}
.btn--add {
  font-size: 1rem;
  box-shadow: 0 0 5px 0 #6e5c5c;
  background: linear-gradient(45deg, #72b359 5%, #186622 100%);
}
.btn:hover {
  background: linear-gradient(to bottom, #408c99 5%, #599bb3 100%);
  background-color: #408c99;
}
.btn-delete {
  margin-left: 0.5rem;
}
input[type="text"] {
  display: block;
  width: 100%;
  max-width: 100%;
  padding: 0.5rem 1rem;
  border: 0;
  border-radius: 0.5rem;
  font-size: 0.875rem;
  line-height: 100%;
  color: var(--dark);
  background-color: #fff;
  transition: all 200ms cubic-bezier(0.42, 0, 0.58, 1);
}
input[type="text"].input-name {
  font-weight: inherit;
  font-size: inherit;
  color: inherit;
  line-height: inherit;
  display: inline-block;
  box-shadow: none;
  border: 0;
}
.options {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 1rem;
  margin-bottom: 1.5rem;
}
.options label {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 1.5rem;
  background-color: #fff;
  border-radius: 0.5rem;
  box-shadow: var(--shadow);
  cursor: pointer;
}
.todo-list .list {
  margin: 1rem 0;
}

.todo-list .todo-item {
  display: flex;
  align-items: center;
  background-color: #fff;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: var(--shadow);
  margin-bottom: 1rem;
}
input[type="checkbox"][id="home"],
input[type="radio"][value="home"] {
  accent-color: rgb(4, 138, 59);
}
input[type="checkbox"][id="business"],
input[type="radio"][value="business"] {
  accent-color: rgb(83, 15, 192);
}

.todo-item label {
  display: block;
  margin-right: 1rem;
  cursor: pointer;
}
.todo-item .todo-content {
  flex: 1 1 0%;
}
.todo-item.done .todo-content input {
  text-decoration: line-through;
  color: var(--grey);
}
</style>
