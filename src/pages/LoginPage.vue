  <template>
    <div class="login">
      <LoginForm @submit="onSubmit" :isError="loginError"/>
    </div>
  </template>

<script>
import axios from 'axios';
import router from "@/router";

import LoginForm from "@/components/LoginForm";

export default {
  name: "LoginPage",
  components: {
    LoginForm,
  },
  data() {
    return {
      userData: null,
      loginError: false,
    }
  },
  mounted() {
    if(localStorage.getItem('userData')) {
      this.redirectToInnerPage();
    }
  },
  methods: {
    redirectToInnerPage() {
      router.push('/inner')
    },

    validateUser(users) {
      const foundedUsersByName = users.filter((user) => user.name === this.userData.name);

      if (foundedUsersByName.length === 0) {
        return this.loginError = true;
      }

      const filterFoundedUserByPhone = foundedUsersByName.filter(user => user.phone.split(' x')[0] === this.userData.phone)

      if (filterFoundedUserByPhone.length === 0) {
        return this.loginError = true;
      }

      window.localStorage.setItem('userData', JSON.stringify(filterFoundedUserByPhone[0]))

      this.redirectToInnerPage();
    },

    getUsers() {
      axios
          .get(`https://jsonplaceholder.typicode.com/users`)
          .then((response) => {
            this.validateUser(response.data);
          })
          .catch((error) => {
            console.log(error);
          });
    },

    onSubmit(user) {
      this.userData = user;
      this.getUsers();

    }
  }
}
</script>
