<template>
  <div>
    <PageHeader>Settings</PageHeader>
    <div class="row">
      <div class="col-md-6 p-1 mb-4 ">
        <div class="border border-success rounded-lg p-3">
          <h4 class="mb-3">User Options</h4>
          <form>
            <div class="form-group">
              <label for="username">Username</label>
              <input type="text" id="username" v-model="usernameInput" class="form-control">
            </div>
            <div class="form-group">
              <label for="avatar">Avatar</label>
              <select name="avatarInput" v-model="avatarInput" id="avatar" class="form-control">
                <option v-for="avatar in avatarOptions" v-bind:key="avatar.index">{{avatar}}</option>
              </select>
            </div>
            <button class="btn btn-success" @click.prevent="updateUserSettings">Update</button>
          </form>
        </div>
      </div>
      <div class="col-md-6 p-1">
        <div class="border border-danger rounded-lg p-3">
          <h4 class="mb-3">Danger Zone</h4>
          <p>These options are irreversible!</p>
          <a
            @click.prevent="deleteAllMessages()" href="#"
            class="btn btn-outline-danger">
            Delete All Messages</a>&nbsp;
          <a
            @click.prevent="deleteUser()" href="#"
            class="btn btn-outline-danger"
          >Delete Account</a>
        </div>
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
      avatarOptions: [
        "coolshades",
        "donut",
        "drums",
        "gnome",
        "plumber",
        "robot",
        "rugby",
        "sailboat",
        "skier",
        "superhero"
      ],
      usernameInput: "",
      avatarInput: "",
      user: ""
    };
  },
  methods: {
    async updateUserSettings() {
      try {
        let data = {
          usernameInput: this.usernameInput,
          avatarInput: this.avatarInput
        };
        await AuthenticationService.updateUserSettings(
          this.$store.state.user,
          data
        );
        this.$router.push({ name: "home" });
      } catch (error) {
        this.error = error.response.data.error;
      }
    },
    async deleteAllMessages(){
      try {
        await AuthenticationService.deleteAllMessages(this.$store.state.user);
        this.$router.push({ name: 'home' });
      } catch (error) {
        this.error = error.response.data.error;
      }
    },
    async deleteUser(){
      try {
        await AuthenticationService.logOut();
        await AuthenticationService.deleteUser(this.$store.state.user);
        this.$store.state.user = "";
        this.$store.state.isUserLoggedIn = false;
        this.$router.push({ name: 'index' });
      } catch (error) {
        this.error = error.response.data.error;
      }
    }
  },
  async created() {
    const user = await AuthenticationService.getUserSettings(
      this.$store.state.user
    );
    const { username, avatar } = user.data[0];
    this.avatarInput = avatar;
    this.usernameInput = username;
  }
};
</script>