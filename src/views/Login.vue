<template>
  <div>
    <h1 class="text-muted">WELCOME TO RECIPES</h1>
    <img class="mb-4" src="../assets/img.jpg" alt="" width="150" height="150" />
    <img
      class="mb-4"
      src="../assets/img2.jpg"
      alt=""
      width="150"
      height="150"
    />
    <img
      class="mb-4"
      src="../assets/img3.jpg"
      alt=""
      width="150"
      height="150"
    />
    <img
      class="mb-4"
      src="../assets/img4.jpg"
      alt=""
      width="150"
      height="150"
    />

    <div class="container">
      <div class="row">
        <div class="col-lg-4 col-md-6 col-sm-8 mx-auto">
          <div class="card login">
            <h1>Sign In</h1>
            <form class="form-group">
              <input
                v-model="user"
                type="text"
                class="form-control"
                placeholder="username"
                aria-label="username"
              />
              <input
                v-model="pass"
                type="password"
                class="form-control"
                placeholder="password"
                aria-label="password"
              />

              <button
                class="w-100 btn btn-lg btn-primary btn-dark"
                type="button"
                v-on:click="authanticate"
              >
                Login
              </button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Vue from "vue";

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
          this.$cookies.set("token", this.token, 15 * 60);
          Vue.alert("You are authenticated", "SUCCESS", "success");
          this.$router.push("/recipes");
        })
        .catch(function (error) {
          console.log(error.toJSON());
          if (error.toJSON().status === 403) {
            Vue.alert("You are not authenticated", "ERROR", "error");
          }
        });
    },
  },
};
</script>
