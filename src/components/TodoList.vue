<template>
  <div class="todo-list">
    <div class="todo-list__filters">
      <div class="todo-list__filter">
        <span>Sort by status:</span>
        <SelectItem class="todo-list__input" :options="statusSelectOptions" :selected="filters.status" @on-select="onStatusFilterChanged"/>
      </div>
      <div class="todo-list__filter">
        <span>Sort by user ID:</span>
        <SelectItem class="todo-list__input" :options="idSelectOptions" :selected="filters.id" @on-select="onIdFilterChanged"/>
      </div>
      <div class="todo-list__filter todo-list__filter--last">
        <span>Search:</span>
        <input class="todo-list__input" type="text" :value="filters.title" @input="onSearchInput">
      </div>
    </div>

    <div class="todo-list__list">
      <div class="todo-list__card" v-for="(todo, index) in todos" :key="index">
        <TodoCard :todo="todo" @favourite="onFavourite"/>
      </div>
    </div>
  </div>
</template>

<script>
import TodoCard from "@/components/common/TodoCard";
import SelectItem from "@/components/common/Select";

export default {
  name: "TodoList",
  props: {
    list: {
      type: Array,
      default: () => [],
    }
  },
  components: {
    TodoCard,
    SelectItem,
  },
  data() {
    return {
      favouriteTodos: null,
      filteredTodos: null,
      filters: {
        title: '',
        status: '',
        id: ''
      },
    }
  },
  computed: {
    todos() {
      if(this.filteredTodos === null) {
        this.getFavourites();
        return this.list
      }

      return this.filteredTodos;
    },
    statusSelectOptions() {
      return ['All', 'Completed', 'Uncompleted', 'Favourites']
    },
    idSelectOptions() {
      const userIds = this.list.map((todo) => todo.userId);

      return ['All Users', ...new Set(userIds)]
    },
  },
  mounted() {
    this.filters.status = this.statusSelectOptions[0];
    this.filters.id = this.idSelectOptions[0];
  },

  methods: {
    getFavourites() {
      this.favouriteTodos = JSON.parse(localStorage.getItem('favourites'))

      if(!this.favouriteTodos) {
        localStorage.setItem('favourites', JSON.stringify([]))
        this.favouriteTodos = JSON.parse(localStorage.getItem('favourites'))
      }

      this.setLocalFavourites(this.list);
    },

    setLocalFavourites(array) {
      array.map(todo => todo.favourite = false)

      this.favouriteTodos.map(id => {
        const matched = array.find(todo => todo.id === id);

        if(matched) {
          matched.favourite = true
        }
      })
    },

    onFavourite(id) {
      if(this.favouriteTodos.length === 0) {
        this.favouriteTodos.push(id);
      }

      if(this.favouriteTodos.length > 0) {
        const indexOfId = this.favouriteTodos.indexOf(id);
        indexOfId < 0 ? this.favouriteTodos.push(id) : this.favouriteTodos.splice(indexOfId, 1)
      }

      localStorage.setItem('favourites', JSON.stringify(this.favouriteTodos))

      this.setLocalFavourites(this.list);

      if(this.filteredTodos !== null) {
        this.setLocalFavourites(this.filteredTodos);
        this.filterTodos(this.filteredTodos, 'status')
      }
    },

    filterTodos(sourcedArray, key) {
      if(key === 'status') {
          switch (this.filters.status) {
            case 'Completed':
              this.filteredTodos = sourcedArray.filter(todo => todo.completed)
              break;
            case 'Uncompleted':
              this.filteredTodos = sourcedArray.filter(todo => !todo.completed)
              break;
            case 'Favourites':
              this.filteredTodos = sourcedArray.filter(todo => todo.favourite)
              break;
            default:
              this.filteredTodos = sourcedArray;
              break;
          }
      }

      if(key === 'id' && this.filters.id !== 'All Users') {
        this.filteredTodos = sourcedArray.filter(todo =>  todo.userId == this.filters.id)
      }

      if(key === 'search' && this.filters.title.length > 0) {
        this.filteredTodos = sourcedArray.filter((todo) => todo.title.includes(this.filters.title));
      }
    },

    onStatusFilterChanged(select) {
      this.filters.status = select;

      if(this.filters.id !== 'All Users' && this.filters.title === '') {
        this.filterTodos(this.list, 'id')
        this.filterTodos(this.filteredTodos, 'status')
      }

      if(this.filters.title !== '' && this.filters.id === 'All Users') {
        this.filterTodos(this.list, 'search')
        this.filterTodos(this.filteredTodos, 'status')
      }

      if(this.filters.id !== 'All Users' && this.filters.title !== '') {
        this.filterTodos(this.list, 'id')
        this.filterTodos(this.filteredTodos, 'search')
        this.filterTodos(this.filteredTodos, 'status')
      }

      if(this.filters.id === 'All Users' && this.filters.title === '') {
        this.filterTodos(this.list, 'status')
      }
    },

    onIdFilterChanged(select) {
      this.filters.id = select;

      if(this.filters.status !== 'All' && this.filters.title === '') {
        this.filterTodos(this.list, 'status')
        this.filterTodos(this.filteredTodos, 'id')
      }

      if(this.filters.title !== '' && this.filters.status === 'All') {
        this.filterTodos(this.list, 'search')
        this.filterTodos(this.filteredTodos, 'id')
      }

      if(this.filters.status !== 'All' && this.filters.title !== '') {
        this.filterTodos(this.list, 'status')
        this.filterTodos(this.filteredTodos, 'search')
        this.filterTodos(this.filteredTodos, 'id')
      }

      if(this.filters.status === 'All' && this.filters.title === '') {
        this.filterTodos(this.list, 'id')
      }
    },

    onSearchInput(e) {
      this.filters.title = e.target.value;

      if(this.filters.status === 'All' && this.filters.id === 'All Users') {
        this.filterTodos(this.list, 'search')
      }

      if(this.filters.status !== 'All' && this.filters.id === 'All Users') {
        this.filterTodos(this.list, 'status')
        this.filterTodos(this.filteredTodos, 'search')
      }

      if(this.filters.status === 'All' && this.filters.id !== 'All Users') {
        this.filterTodos(this.list, 'id')
        this.filterTodos(this.filteredTodos, 'search')
      }

      if(this.filters.status !== 'All' && this.filters.id !== 'All Users') {
        this.filterTodos(this.list, 'status')
        this.filterTodos(this.filteredTodos, 'id')
        this.filterTodos(this.filteredTodos, 'search')
      }
    },
  }
}
</script>
