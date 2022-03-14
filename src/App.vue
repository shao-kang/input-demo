<script>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup

import Input from "./components/Input.vue";
import { defineComponent } from "vue";
export default defineComponent({
  data() {
    return {
      users: [
        {
          name: "1212",
          id: "dsdfds",
          img: "dfds",
        },
        {
          name: "12dsfdslkfjdslkfjdslkdjsklfjdskljdk12",
          id: "dsdfds",
          img: "dfds",
        },
        {
          name: "1afdsfdsaf2",
          id: "dsdfds",
          img: "dfds",
        },
        {
          name: "abckddkd",
          id: "dsdfds",
          img: "dfds",
        },
        {
          name: "fdsff12",
          id: "dsdfds",
          img: "dfds",
        },
        {
          name: "1fffffff212",
          id: "dsdfds",
          img: "dfds",
        },
        {
          name: "1kkkkkk212",
          id: "dsdfds",
          img: "dfds",
        },
      ],
    };
  },
  methods: {
    change(e) {
      console.log("change", e);
    },
    changeUserName(e) {
      console.log("changeUserName", e);
    },
  },
  components: {
    Input,
  },
});
</script>

<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <div></div>
  <Input @change="change" @changeUserName="changeUserName" :users="users" />
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
