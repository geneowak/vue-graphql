<template>
  <form @submit.prevent="submit">
    <fieldset>
      <input type="text" v-model="name" placeholder="Name" required />
      <input type="text" v-model="twitter" placeholder="Twitter" required />
      <select v-model="rocket" required>
        <option value="" selected>Choose Rocket</option>
        <option v-for="rocket in rockets" :value="rocket.name" :key="rocket.id">
          {{ rocket.name }}
        </option>
      </select>
      <input type="submit" value="Send" />
    </fieldset>
  </form>
</template>

<script>
import uuidv4 from "uuid/v4";
import { INSERT_USER } from "@/mutations.js";
import { GET_ROCKETS, GET_USERS } from "../queries";

export default {
  apollo: {
    rockets: {
      query: GET_ROCKETS,
    },
  },
  data() {
    return {
      name: "",
      twitter: "",
      rocket: "",
    };
  },
  methods: {
    submit() {
      const { name, twitter, rocket } = this.$data;
      const id = uuidv4();
      this.$apollo
        .mutate({
          mutation: INSERT_USER,
          variables: { id, name, twitter, rocket },
          // refetch users to get the newly added one
          // refetchQueries: ["getUsers"],
          // update the cached data to prevent refetching
          update: (cache, { data: { insert_users } }) => {
            const [newUser] = insert_users.returning;
            const data = cache.readQuery({ query: GET_USERS });
            // insert the user at the top of the list
            data.users.unshift(newUser);
            // remove last user if there are now more than 10 users
            if (data.users.length > 10) {
              data.users.pop();
            }
            cache.writeQuery({ query: GET_USERS, data });
          },
          // mock the response while waiting for the mutation to complete
          optimisticResponse: {
            __typename: "Mutation",
            insert_users: {
              __typename: "users_mutation_response",
              returning: [
                { __typename: "users", id: -1, name, twitter, rocket },
              ],
            },
          },
        })
        .then((data) => {
          console.log("Users added successfully", data);
        })
        .catch((err) => {
          console.error(err);
        })
        .finally(() => {
          Object.assign(this.$data, { name: "", twitter: "", rocket: "" });
        });
    },
  },
};
</script>
