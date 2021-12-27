<template>
  <div>
    <div class="container p-1 p-md-3 mb-1 text-white rounded bg-dark">
      <div class="row">
        <div class="col">
          <div class="input-group mb-3">
            <input
              type="text"
              class="form-control"
              placeholder=""
              id="txtSearch"
              v-model="recipeName"
            />

            <button
              class="btn btn-outline-secondary btn-dark"
              type="button"
              id="btnSearch"
              v-on:click="getRecipe"
            >
              Search
            </button>
          </div>
        </div>

        <div class="col"></div>
      </div>
    </div>

    <br />

    <div class="container">
      <table class="table table-dark table-striped" id="recipesTable">
        <thead>
          <tr>
            <th scope="col"></th>
            <th scope="col">ID</th>
            <th scope="col">Name</th>
            <th scope="col">Create Date</th>
            <th scope="col">Suitable For Vegs :)</th>
            <th scope="col">Suitable People Count</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="recipe in recipes" :key="recipe.recipeID">
            <td scope="row">
              <input
                class="form-check"
                name="grid-radios"
                type="radio"
                value="false"
                v-bind:id="recipe.recipeID"
                v-on:click="getDetails"
              />
            </td>
            <td scope="row">{{ recipe.recipeID }}</td>
            <td scope="row">{{ recipe.recipeName }}</td>
            <td scope="row">{{ recipe.createDate }}</td>
            <td scope="row">{{ recipe.vegetarian ? "YES" : "NO" }}</td>
            <td scope="row">{{ recipe.suitablePeopleCount }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="container p-4 p-md-5 mb-4 text-white rounded bg-dark" v-if="this.instructions" >
      <p>{{ this.instructions }}</p>
    </div>

    <div class="container" v-if="ingredients">
    <h5 v-if="ingredients[0]">Ingredients</h5>
      <ul class="list-group" >
         
         <li class="list-group-item" v-for="ing in ingredients" :key="ing">{{ ing }}</li>
     </ul> 
    
    
    </div>

    <div class="container">
      <div class="btn-group" role="group" aria-label="Basic outlined example">
        <button type="button" class="btn btn-outline-primary">Left</button>
        <button type="button" class="btn btn-outline-primary">Middle</button>
        <button type="button" class="btn btn-outline-primary">Right</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      recipes: null,
      recipeName: null,
      instructions: null,
      ingredients: null,
    };
  },
  created: function () {
    axios.get("http://localhost:8081/api/recipe/getAll").then((res) => {
      this.recipes = res.data;
      console.log(this.recipes);
    });
  },

  methods: {
    getRecipe: function () {
      axios
        .get("http://localhost:8081/api/recipe/get", {
          params: { recipeName: this.recipeName },
        })
        .then((res) => {
          this.recipes = [res.data];
          this.instructions = null;
          this.ingredients = null;
        });
    },
    getDetails: function (event) {
      console.log(event);

      console.log(
        this.recipes.filter(
          ({ recipeID }) => recipeID == event.srcElement.id
        )[0]
      );

      this.ingredients = this.recipes.filter(
        ({ recipeID }) => recipeID == event.srcElement.id
      )[0].ingredientList;
      this.instructions = this.recipes.filter(
        ({ recipeID }) => recipeID == event.srcElement.id
      )[0].instructions;
    },
  },
};
</script>
