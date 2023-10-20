<template>
  <div
      class="todo-card"
      :class="{
    'todo-card--completed': todo.completed,
  }"
  >
    <div class="todo-card__header">
      <span>user id: {{todo.userId}}</span>
      <div @click="onFavourite">
        <StarIcon
            class="todo-card__star"
            :class="{
              'todo-card__star--filled': todo.favourite
          }"
        />
      </div>
    </div>

    <div class="todo-card__content">
      <p class="todo-card__font todo-card__font--title">{{todo.title}}</p>
    </div>
  </div>
</template>

<script>
import StarIcon from "@/components/ui/StarIcon";

export default {
  name: "TodoCard",
  props: {
    todo: {
      type: Object,
      required: true,
    }
  },
  components: {
    StarIcon
  },
  methods: {
    onFavourite() {
      this.$emit('favourite', this.todo.id)
    }
  }
}
</script>
