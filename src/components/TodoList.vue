<template>
  <div class="todo-list">
    <div class="todo-list__card" v-for="(todo, index) in list" :key="index">
      <TodoCard :todo="todo"/>
    </div>
  </div>
</template>

<script>
import TodoCard from "@/components/common/TodoCard";

export default {
  name: "TodoList",
  props: {
    list: {
      type: Array,
      default: () => [],
    }
  },
  components: {
    TodoCard
  },
}
</script>
