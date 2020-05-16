<template>
  <div id="app">
    <form @submit.prevent="handleSubmit">
      <label>First</label>
      <v-select
        label="name"
        :filterable="false"
        :options="options"
        @search="onSearch"
      >
        <template slot="no-options">
          type to search GitHub repositories..
        </template>
        <template slot="option" slot-scope="option">
          <div class="d-center">
            <img :src="option.owner.avatar_url" />
            {{ option.full_name }}
          </div>
        </template>
        <template slot="selected-option" slot-scope="option">
          <div class="selected d-center">
            <img :src="option.owner.avatar_url" />
            {{ option.full_name }}
          </div>
        </template>
      </v-select>
      <button type="submit">Search</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";
import _ from 'lodash'

export default {
  name: "App",
  methods: {
    handleSubmit() {
      axios.post("http://localhost:5000").then(res => {
        console.log("axios", res.data);
      });
    },
    onSearch(search, loading) {
      loading(true);
      this.search(loading, search, this);
    },
    search: _.debounce((loading, search, vm) => {
      fetch(
              `https://api.github.com/search/repositories?q=${escape(search)}`
      ).then(res => {
        res.json().then(json => (vm.options = json.items));
        loading(false);
      });
    }, 350)
  },
  data() {
    return {
      options: [],
    };
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  justify-content: center;
}

form {
  max-width: 800px;
  width: 100%;
}

input {
  width: 100%;
}

img {
  height: auto;
  max-width: 2.5rem;
  margin-right: 1rem;
}

.d-center {
  display: flex;
  align-items: center;
}

.selected img {
  width: auto;
  max-height: 23px;
  margin-right: 0.5rem;
}

.v-select .dropdown li {
  border-bottom: 1px solid rgba(112, 128, 144, 0.1);
}

.v-select .dropdown li:last-child {
  border-bottom: none;
}

.v-select .dropdown li a {
  padding: 10px 20px;
  width: 100%;
  font-size: 1.25em;
  color: #3c3c3c;
}

.v-select .dropdown-menu .active > a {
  color: #fff;
}
</style>
