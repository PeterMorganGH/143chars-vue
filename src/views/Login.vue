<template>
  <div>
    <PageHeader>Log In</PageHeader>
    <div class="row">
      <div class="col">
        <form>
          <div class="form-group">
            <label for="username">Username</label>
            <input
              type="text"
              id="username"
              placeholder="Enter desired username"
              v-model="usernameInput"
              class="form-control"
            >
          </div>
          <div class="form-group">
            <label for="password">Password</label>
            <input
              type="password"
              id="password"
              placeholder="Enter desired password"
              v-model="passwordInput"
              class="form-control"
            >
          </div>
          <div class="form-group"></div>
          <button class="btn btn-primary" @click.prevent="login">Log In</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import AuthenticationService from "@/services/AuthenticationService";
import PageHeader from '@/components/PageHeader';

export default {
  components: {
    PageHeader
  },
  data() {
    return {
      usernameInput: "",
      passwordInput: ""
    };
  },
  methods: {
    async login() {
      try {
        const response = await AuthenticationService.login({
          usernameInput: this.usernameInput,
          passwordInput: this.passwordInput,
        });
        this.$store.dispatch('setUser', response.data.id);
        this.$router.push({ name: 'home' });
      } catch (error) {
        this.error = error.response.data.error;
      }
    }
  }
};
</script>

<style>
</style>
