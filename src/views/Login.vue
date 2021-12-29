<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-5">
          <div class="input-group mb-3">
            <input
              v-model="user"
              type="text"
              class="form-control"
              placeholder="username"
              aria-label="username"
              aria-describedby="button-addon2"
            />
          </div>
        </div>
      </div>

      <div class="row">
        <div class="col-5">
          <div class="input-group mb-3">
            <input
              v-model="pass"
              type="password"
              class="form-control"
              placeholder="password"
              aria-label="password"
            />
            <button
              class="btn btn-outline-secondary btn-dark"
              type="button"
              id="btnSearch"
              v-on:click="authanticate"
            >
              Login
            </button>
          </div>
        </div>
      </div>
    </div>

  </div>

  
</template>

<script>
import axios from "axios";
import Vue from 'vue';

export default {
  data() {
    return {
      pass: null,
      user: null,
      authenticated: null,
      token: null,
    };
  },
  created: function () {},

  methods: {
    authanticate: function () {
      axios
        .post("http://localhost:8081/authenticate", {
          username: this.user,
          password: this.pass,
        })
        .then((res) => {
      
          this.token = res.data.jwttoken;
          console.log(this.token);
          this.$cookies.set('token',this.token, 15*60);
          Vue.alert("You are authenticated", "SUCCESS", "success");
          this.$router.push("/recipes");
        })
        .catch(function (error) {
          console.log(error.toJSON());
          if(error.toJSON().status === 403){
              Vue.alert("You are not authenticated", "ERROR", "error");
          }
        });


    },
  },
};
</script>
