<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to the Places App!",
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      editPlaceParams: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/places").then((response) => {
        console.log("places index", response);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("/places", this.newPlaceParams)
        .then((response) => {
          console.log("places create", response);
          // this.places.push(response.data);
          this.newPlaceParams = {};
        })
        .catch((error) => {
          console.log("places create error", error.response);
        });
    },
    showPlace: function (place) {
      this.currentPlace = place;
      this.editPlaceParams = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      axios
        .patch("/places" + place.id, this.editPlaceParams)
        .then((response) => {
          console.log("places update", response);
          this.currentPlace = {};
        })
        .catch((error) => {
          console.log("places update error", error.response);
        });
    },
    destroyPlace: function (place) {
      axios.delete("/places" + place.id).then((response) => {
        console.log("destroy  place", response);
        var index = this.places.indexOf(place);
        this.photos.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>New Place</h2>
    <div>
      Name:
      <input type="text" v-model="newPlaceParams.name" />
      Address:
      <input type="text" v-model="newPlaceParams.address" />
      <br />
      <button v-on:click="createPlace()">Create Place</button>
    </div>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place info</h1>
        <input type="text" v-model="editPlaceParams.name" />
        <input type="text" v-model="editPlaceParams.address" />
        <br />
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Destory?!</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
