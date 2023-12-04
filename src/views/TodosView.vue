<script setup>
import { computed, ref, watch } from "vue";
import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";
import { uid } from "uid";
import { Icon } from "@iconify/vue";

const todoList = ref([]);

const todosCompleted = computed(() => {
  return todoList.value.every((item) => item.isCompleted);
});

watch(
  todoList,
  () => {
    saveTodoList();
  },
  { deep: true }
);

const saveTodoList = () => {
  localStorage.setItem("todolist", JSON.stringify(todoList.value));
};

const fetchTodoList = () => {
  const data = JSON.parse(localStorage.getItem("todolist"));
  console.log({ data });
  if (data) {
    todoList.value = data;
  }
};

fetchTodoList();

const handleAdd = (todo) => {
  todoList.value.push({ id: uid(), todo, isCompleted: null, isEditing: null });
};

const toggleEdit = (id) => {
  const index = todoList.value.findIndex((item) => item.id === id);
  if (index >= 0) {
    todoList.value[index].isEditing = !todoList.value[index].isEditing;
  }
};

const toggleComplete = (id) => {
  const index = todoList.value.findIndex((item) => item.id === id);
  if (index >= 0) {
    todoList.value[index].isCompleted = !todoList.value[index].isCompleted;
  }
};

const handleUpdate = (text, id) => {
  const index = todoList.value.findIndex((item) => item.id === id);
  if (index >= 0) {
    todoList.value[index].todo = text;
  }
};

const handleDelete = (id) => {
  todoList.value = todoList.value.filter((item) => item.id !== id);
};
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @submit="handleAdd" />
    <ui class="todo-list" v-if="todoList.length > 0">
      <TodoItem
        v-for="todo in todoList"
        :key="todo.id"
        :todo="todo"
        @edit="toggleEdit"
        @update="handleUpdate"
        @delete="handleDelete"
        @complete="toggleComplete"
      />
    </ui>
    <p v-else class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" />
      <span>You have no todo's to complete! Add one! </span>
    </p>
    <p v-if="todosCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>
<style scoped lang="scss">
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>