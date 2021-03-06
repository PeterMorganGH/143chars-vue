<template>
  <div>
    <PageHeader>
      {{user}}'s Profile
      <small v-if="messageCount === 1">{{messageCount}} Message</small>
      <small v-else>{{messageCount}} Messages</small>
    </PageHeader>
    <div class="row">
      <div class="col">
        <ul class="list-unstyled mt-2">
          <li class="media mb-4" v-for="message in messages.data" v-bind:key="message.index">
            <img :src="`avatars/${message.user.avatar}.svg`" width="64" height="64" class="mr-3">
            <div class="media-body">
              <h5 class="mt-0 mb-1">{{message.message}}</h5>Posted
              <span v-if="!currentUser">
                by
                <a :href="'#/profile/' + message.user._id">@{{message.user.username}}</a>&nbsp;
              </span>
              <span
                :title="moment(message.date).format('MMMM D, YYYY h:mma')"
              >{{moment(message.date).fromNow()}}</span>&nbsp;
              <a
                v-if="currentUser"
                class="badge badge-danger"
                href="#"
                @click.prevent="deleteMsg(message._id)"
              >Delete</a>
            </div>
          </li>
        </ul>
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
      loggedIn: this.$store.state.isUserLoggedIn,
      user: "",
      messageCount: "",
      messages: [],
      currentUser: false
    };
  },
  async created() {
    this.getData();
    this.getCurrentUser();
  },
  methods: {
    getData: async function() {
      const messages = await AuthenticationService.getUserMessages(
        this.$route.params.userId
      );
      this.messageCount = messages.data.length;
      this.messages = messages;
      const userData = await AuthenticationService.getUserSettings(
        this.$route.params.userId
      );
      this.user = userData.data[0].username;
    },
    getCurrentUser: function() {
      if (this.$route.params.userId === this.$store.state.user) {
        this.currentUser = true;
      }
    },
    async deleteMsg(messageId) {
      try {
        await AuthenticationService.deleteMsg(messageId);
        this.$router.push({ name: "home" });
      } catch (error) {
        this.error = error.response.data.error;
      }
    }
  },
  watch: {
    "$route.params.userId": function() {
      this.getData();
      this.getCurrentUser();
    }
  }
};
</script>
