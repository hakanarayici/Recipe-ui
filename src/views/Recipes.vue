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

    <div
      class="container p-4 p-md-5 mb-4 text-white rounded bg-dark"
      v-if="this.instructions"
    >
      <p>{{ this.instructions }}</p>
    </div>

    <div
      class="container p-4 p-md-5 mb-4 text-white rounded bg-dark"
      v-if="ingredients"
    >
      <h5 v-if="ingredients[0]">Ingredients</h5>
      <ul class="list-group">
        <li class="list-group-item" v-for="ing in ingredients" :key="ing">
          {{ ing }}
        </li>
      </ul>
    </div>

    <br />

    <div class="container">
      <div class="row">
        <div class="col-10"></div>
        <div class="col-2">
          <div class="btn-group" role="group" align="right">
            <button
              type="button"
              class="btn btn-outline-primary"
              v-on:click="addRecipe"
            >
              Add
            </button>
            <button
              type="button"
              class="btn btn-outline-primary"
              :disabled="!selectedRecipe"
              v-on:click="updateRecipe"
            >
              Update
            </button>
            <button
              type="button"
              class="btn btn-outline-primary"
              v-on:click="deleteRecipe"
              :disabled="!selectedRecipe"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>

    <br />
    <br />
    <br />

    <div
      class="container p-1 p-md-3 mb-1 text-white rounded bg-dark"
      v-if="saveState == 1 || saveState == 2"
    >
      <div class="row">
        <div class="col-4">
          <input
            type="text"
            class="form-control"
            id="saveRecipeName"
            placeholder="Recipe Name"
            v-model="recipeNameToSave"
          />
        </div>
        <div class="col-1"></div>

        <div class="col-2">
          <div class="form-check">
            <label class="form-check-label" for="saveVegetariansCheck"
              >Suitable for Vegetarians</label
            >
            <input
              type="checkbox"
              class="form-check-input"
              id="saveVegetariansCheck"
              v-model="suitableForVegetariansToSave"
            />
          </div>
        </div>

        <div class="col-1"></div>

        <div class="col-4">
          <div class="row">
            <div class="col-6">
              <input
                type="number"
                id="saveSuitablePeopleCount"
                class="form-control"
                value="1"
                min="0"
                max="1000"
                step="1"
                v-model="suitablePeopleCountToSave"
              />
            </div>
            <div class="col-5">
              <label class="form-label" for="saveSuitablePeopleCount"
                >Suitable people count</label
              >
            </div>
          </div>
        </div>
      </div>

      <br />
      <br />

      <div class="row">
        <div class="col-7">
          <div class="row">
            <div class="col-12">
              <table
                class="table table-dark table-striped"
                id="ingredientTable"
              >
                <thead>
                  <tr>
                    <th scope="col">Ingredients</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="ingredient in ingredientsToSave" :key="ingredient">
                    <td scope="row">{{ ingredient }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
          <div class="row">
            <div class="input-group mb-3">
              <input
                v-model="ingredientToAdd"
                type="text"
                class="form-control"
                placeholder="Ingredient"
                aria-describedby="basic-addon2"
              />
              <div class="input-group-append">
                <button
                  class="btn btn-outline-secondary"
                  type="button"
                  v-on:click="addIngredient"
                >
                  Add Ingredient
                </button>
              </div>
            </div>
          </div>
        </div>

        <div class="col-1"></div>

        <div class="col-4 text-right">
          <textarea
            class="form-control"
            id="saveInsturctionsTxtArea"
            rows="6"
            placeholder="Instructions"
            v-model="instructionsToSave"
          ></textarea>
        </div>
      </div>

      <br />
      <div class="row">
        <div class="col-10"></div>

        <div class="col-2">
          <div class="row text-right"></div>
          <div class="col-3"></div>
          <div class="col-9">
            <div class="input-group">
              <button
                class="btn btn-outline-secondary"
                type="button"
                v-on:click="cancelSave"
              >
                Cancel
              </button>
              <button
                class="btn btn-outline-secondary"
                type="button"
                v-on:click="saveRecipe"
              >
                Save
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import Vue from "vue";

export default {
  data() {
    return {
      recipes: null,
      recipeName: null,
      instructions: null,
      ingredients: null,
      selectedRecipe: null,
      ingredientsToSave: [],
      recipeNameToSave: null,
      suitableForVegetariansToSave: null,
      suitablePeopleCountToSave: null,
      instructionsToSave: null,
      ingredientToAdd: null,
      saveState: 0,
    };
  },
  created: function () {

    if(!this.$cookies.get('token')){
      Vue.alert("You shall not pass!", "ERROR", "error");
      this.$router.push("/");
    }else{
      this.getAllRecipes();
    }

  },

  methods: {
    getAllRecipes: function () {
      const headers = {
        Authorization: "Bearer " + this.$cookies.get("token"),
        hakan: "arayici",
      };

      // console.log(headers);

      axios
        .get("http://localhost:8081/api/recipe/getAll", { headers })
        .then((res) => {
          this.recipes = res.data;
          console.log(this.recipes);
        });
    },

    getRecipe: function () {
      if (this.recipeName === "") {
        this.getAllRecipes();
      } else {
        axios
          .get("http://localhost:8081/api/recipe/get", {
            params: { recipeName: this.recipeName },
            headers: {
              Authorization: "Bearer " + this.$cookies.get("token"),
              hakan: "arayici",
            },
          })
          .then((res) => {
            this.recipes = res.data ? [res.data] : [];
            this.instructions = null;
            this.ingredients = null;
            this.selectedRecipe = null;
          });
      }
    },
    getDetails: function (event) {
      this.selectedRecipe = this.recipes.filter(
        ({ recipeID }) => recipeID == event.srcElement.id
      )[0];

      this.ingredients = this.selectedRecipe.ingredientList;
      this.instructions = this.selectedRecipe.instructions;
    },

    addRecipe: function () {
      this.saveState = 1;

      this.recipeNameToSave = null;
      this.suitableForVegetariansToSave = null;
      this.suitablePeopleCountToSave = null;
      this.ingredientsToSave = [];
      this.instructionsToSave = null;
    },
    updateRecipe: function () {
      this.saveState = 2;
      this.recipeNameToSave = this.selectedRecipe.recipeName;
      this.suitableForVegetariansToSave = this.selectedRecipe.vegetarian;
      this.suitablePeopleCountToSave = this.selectedRecipe.suitablePeopleCount;
      this.ingredientsToSave = this.ingredients;
      this.instructionsToSave = this.selectedRecipe.instructions;
    },
    deleteRecipe: function () {
      axios
        .delete("http://localhost:8081/api/recipe/delete", {
          params: { recipeID: this.selectedRecipe.recipeID },
          headers: {
            Authorization: "Bearer " + this.$cookies.get("token"),
            hakan: "arayici",
          },
        })
        .then(() => {
          this.instructions = null;
          this.ingredients = null;
          this.selectedRecipe = null;

          Vue.alert("Recipe is Deleted", "SUCCESS", "success");

          this.getAllRecipes();
        })
        .catch(function (error) {
          console.log(error.toJSON());
          alert("error");
        });
    },

    addIngredient: function () {
      this.ingredientsToSave.push(this.ingredientToAdd);
    },

    saveRecipe: function () {

       const headers = {
          Authorization: "Bearer " + this.$cookies.get("token"),
          hakan: "arayici",
        };


      // add recipe
      if (this.saveState === 1) {
       

        axios
          .post(
            "http://localhost:8081/api/recipe/create",
            {
              createDate: moment(String(new Date())).format("DD-MM-YYYY HH:mm"),
              recipeName: this.recipeNameToSave,
              vegetarian: this.suitableForVegetariansToSave,
              suitablePeopleCount: this.suitablePeopleCountToSave,
              instructions: this.instructionsToSave,
              ingredientList: this.ingredientsToSave,
            },
            { headers }
          )
          .then((res) => {
            console.log(res);
            this.getAllRecipes();

            this.saveState = 0;
            Vue.alert("Recipe is Added", "SUCCESS", "success");
          })
          .catch(function (error) {
            console.log(error.toJSON());

            Vue.alert(
              "Recipe is not Added, Something went wrong",
              "SUCCESS",
              "error"
            );
          });
      } else if (this.saveState === 2) {
        //update
        axios
          .put("http://localhost:8081/api/recipe/update", {
            recipeID: this.selectedRecipe.recipeID,
            createDate: this.selectedRecipe.createDate,
            recipeName: this.recipeNameToSave,
            vegetarian: this.suitableForVegetariansToSave,
            suitablePeopleCount: this.suitablePeopleCountToSave,
            instructions: this.instructionsToSave,
            ingredientList: this.ingredientsToSave,
          },{headers})
          .then((res) => {
            console.log(res);
            this.getAllRecipes();
            Vue.alert("Recipe is Updated");
            this.saveState = 0;
          })
          .catch(function (error) {
            console.log(error.toJSON());

            alert("could not add");
          });
      }
    },
    cancelSave: function () {
      this.saveState = 0;
    },
  },
};
</script>
