<template>
  <p>Patch</p>
  <div class="getList" v-if="usersList">
    <div class="column">
      <button @click="expand(user)" v-for="user in usersList" :key="user.id">
        {{ user.username }}
      </button>
    </div>
  </div>
  <div>
    <form v-if="selectedUser" @submit.prevent="submitForm">
      <label for="username">Username</label>
      <input v-model="selectedUser.username" />
      <label for="email">Email</label>
      <input v-model="selectedUser.email" />
      <button type="submit">Submit</button>
    </form>
  </div>
  <div v-if="message">
    <h4>{{ message }}</h4>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import { defineComponent } from "vue";
import { FEenv } from "@/env";
import { UserInterface } from "@/models/User.interface";

export default defineComponent({
  name: "HTTPPatch",
  data() {
    return {
      usersList: [] as UserInterface[],
      devurl: new FEenv(),
      selectedUser: null as UserInterface | null,
      message: "",
    };
  },
  methods: {
    async getList() {
      this.usersList = (
        await axios.get(this.devurl.devbaseurl + "users/getAll")
      ).data;
    },
    expand(user: UserInterface) {
      this.selectedUser = { ...user };
    },
    async submitForm() {
      if (this.selectedUser) {
        const userId = this.selectedUser.id;
        this.message = (
          await axios.patch(
            this.devurl.devbaseurl + "users/patch",
            this.selectedUser
          )
        ).data.message;
        this.selectedUser = null;
        this.getList();
        setTimeout(() => {
          this.message = "";
        }, 5000);
      }
    },
  },
  mounted() {
    this.getList();
  },
});
</script>

<style scoped>
.getList {
  display: flex;
}
.column {
  flex: 1;
  max-width: 100px;
}
button {
  margin-bottom: 10px;
}
</style>
