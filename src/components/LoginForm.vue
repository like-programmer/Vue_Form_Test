<template>
  <form class="form">
    <div class="form__header">
      <span class="form__font form__font--header">Login</span>
    </div>

    <fieldset class="form__fieldset">
      <div class="form__legend">
        <span class="form__font form__font--legend">description</span>
      </div>

      <input class="form__input" type="text" placeholder="Username" v-model="userName">

      <input class="form__input" type="text" placeholder="Phone Number" v-model="phone">

      <ButtonItem @click="onSubmit">
        <span class="form__font form__font--button">Login</span>
      </ButtonItem>

      <div v-if="isError" class="form__error">
        <span class="form__font form__font--error">Login error</span>
      </div>
    </fieldset>
  </form>
</template>

<script>
import ButtonItem from "@/components/common/Button";

export default {
  name: "LoginForm",
  props: {
    isError: {
      type: Boolean,
      default: false,
    }
  },
  components: {
    ButtonItem
  },
  data() {
    return {
      userName: '',
      phone: ''
    }
  },
  watch: {
    userName(newName) {
      this.userName = newName.replace(/[^a-zA-zа-яА-Я\s]/, '')
    },
    phone(newPhone) {
      this.phone = newPhone.replace(/[^.0-9-()+\s]/, '')
    }
  },
  methods: {
    onSubmit() {
      this.$emit('submit', {name: this.userName, phone: this.phone})
    }
  }
}
</script>
