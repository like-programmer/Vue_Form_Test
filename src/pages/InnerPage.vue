<template>
<div class="inner-page">
  <PageHeader :userData="userData"/>

  <div class="inner-page__container">
    <ButtonItem class="inner-page__button">Add Todo</ButtonItem>

    <div class="inner-page__filters">
      <div class="inner-page__filter">
        <span>Sort by status:</span>
        <SelectItem class="inner-page__input" :options="statusSelectOptions"/>
      </div>
      <div class="inner-page__filter">
        <span>Sort by user ID:</span>
        <SelectItem class="inner-page__input" :options="idSelectOptions"/>
      </div>
      <div class="inner-page__filter inner-page__filter--last">
        <span>Search:</span>
        <input class="inner-page__input" type="text">
      </div>
    </div>

    <TodoList :list="todos"/>
  </div>
</div>
</template>

<script>
import PageHeader from "@/components/PageHeader";
import TodoList from "@/components/TodoList";
import SelectItem from "@/components/common/Select";
import ButtonItem from "@/components/common/Button";
import axios from "axios";

export default {
  name: "InnerPage",
  components: {
    PageHeader,
    TodoList,
    SelectItem,
    ButtonItem,
  },
  data() {
    return {
      userData: null,
      todos: [],
    }
  },
  computed: {
    statusSelectOptions() {
      return ['All', 'Completed', 'Uncompleted', 'Favourites']
    },
    idSelectOptions() {
      //TODO: Refactor
      const userIds = this.todos.map((todo) => todo.userId)

      return ['All Users', ...userIds]
    }
  },
  mounted() {
    this.userData = JSON.parse(localStorage.getItem('userData'))
    this.getTodos();
  },
  methods: {
    getTodos() {
      axios
          .get(`https://jsonplaceholder.typicode.com/todos`)
          .then((response) => {
            this.todos = response.data
          })
          .catch((error) => {
            console.log(error);
          });
    }
  }
}
</script>
