<template>
<div class="inner-page">
  <PageHeader :userData="userData"/>

  <div class="inner-page__container">

    <ButtonItem v-if="!showFrom" class="inner-page__button inner-page__button--add" @click="showFrom = true">Add Todo</ButtonItem>

    <div v-else class="inner-page__add-form">
      <div class="inner-page__form-input">
        <span>User id</span>
<!--  Ниже поставлен тег disabled потому что, так как по тз там должен находиться userId - меня смутило,
      что его можно менять, так как это идентификатор пользователя. Следовательно он выводится, как написано в ТЗ
      но только для чтения -->
        <input class="inner-page__input inner-page__input--id" type="text" disabled :value="newTodo.userId">
      </div>

      <div class="inner-page__form-input">
        <span>Todo title</span>
        <input class="inner-page__input" type="text" v-model="newTodo.title">
      </div>

      <ButtonItem class="inner-page__button inner-page__button--submit" @click="onSubmit" :disabled="isSubmitDisabled">Add</ButtonItem>
      <ButtonItem class="inner-page__button" btnStyle="secondary" @click="showFrom = false">Close</ButtonItem>
    </div>

    <TodoList :list="todos"/>
  </div>
</div>
</template>

<script>
import PageHeader from "@/components/PageHeader";
import TodoList from "@/components/TodoList";
import ButtonItem from "@/components/common/Button";
import axios from "axios";

export default {
  name: "InnerPage",
  components: {
    PageHeader,
    TodoList,
    ButtonItem,
  },
  data() {
    return {
      userData: null,
      todos: [],
      showFrom: false,
      newTodo: {
        id: null,
        userId: null,
        title: '',
        completed: false,
        favourite: false,
      },
    }
  },
  computed: {
    isSubmitDisabled() {
      return this.newTodo.title.length === 0;
    }
  },
  mounted() {
    this.userData = JSON.parse(localStorage.getItem('userData'))
    this.newTodo.userId = this.userData.id;
    this.getTodos();
  },
  methods: {
    async getTodos() {
      await axios
          .get(`https://jsonplaceholder.typicode.com/todos`)
          .then((response) => {
            response.data.map((todo) => todo.favourite = false);

            this.todos = response.data;
          })
          .catch((error) => {
            console.log(error);
          })
      return true;
    },

    async onSubmit() {
      this.newTodo.id = this.todos.length + 1;

      await axios
          .post(`https://jsonplaceholder.typicode.com/todos`, this.newTodo)
          .then((response) => {
            if(response.status === 201) {
              this.showFrom = false;

              this.todos.push(this.newTodo);

              this.newTodo = {
                id: null,
                userId: this.userData.id,
                title: '',
                completed: false,
                favourite: false,
              };
            }
          })
          .catch((error) => {
            console.log(error);
          })
      return true;
    },
  }
}
</script>
