<template>
  <div id="Home">
    <v-card-title class="title">
      Procura de usuarios
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      dense
      :headers="headers"
      :items="items"
      item-key="login"
      class="elevation-1 tabela"
      :search="search"
      :custom-filter="filterUser"
      :loading="count !== 0"
      loading-text="Loading"
      :footer-props="{
        'items-per-page-options': [20, 30, 40, 50],
      }"
    >
      <template v-slot:[`item.avatar_url`]="{ item }">
        <img class="avatar" :src="item.avatar_url" alt="" />
      </template>
      <template v-slot:[`item.html_url`]="{ item }">
        <a :href="item.html_url">{{ item.html_url }}</a>
      </template>
      <template slot="no-data">
        <v-alert :value="true && !requestErro" color="error">
          Nenhum registro encontrado
        </v-alert>
        <v-alert :value="requestErro" color="error"> Erro na chamada </v-alert>
      </template>
    </v-data-table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "HomePage",
  data: () => ({
    search: "",
    loading: false,
    items: [],
    count: 0,
    requestErro: false,
  }),
  created() {
    this.getUsers();
  },
  computed: {
    headers() {
      return [
        {
          text: "Avatar",
          sortable: false,
          value: "avatar_url",
          align: "center",
          width: "100px",
        },
        { text: "Usuario", sortable: true, value: "login", align: "left" },
        { text: "Link", sortable: false, value: "html_url", align: "left" },
      ];
    },
  },
  methods: {
    filterUser(value, search) {
      return (
        value != null &&
        search != null &&
        typeof value === "string" &&
        value.toString().toLowerCase().indexOf(search.toLowerCase()) !== -1
      );
    },
    async getUsers() {
      this.loading = true;
      this.requestErro = false;
      this.users = [];
      let baseURL = "https://api.github.com";
      try {
        await axios.get(baseURL + "/users").then((response) => {
          const data = response.data;
          console.log(data);
          this.items = data;
          this.loading = false;
        });
      } catch (error) {
        this.requestErro = true;
        this.loading = false;
      }
    },
  },
};
</script>

<style lang="sass">
#Home
	.title
		width: 60%
		margin: 0 auto
	.tabela
		width: 60%
		margin: 0 auto
	.avatar
		width: 50px
</style>
