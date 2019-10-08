<template>
  <div class="home">
    <h1>Add another Place</h1>
    <div>
      Name: <input type="text" v-model="newPlaceName">
      Address: <input type="text" v-model="newPlaceAddress">
      <button v-on:click="createPlace()">Terraform this bitch</button>
    </div>

    <h1>All Places</h1>
    <div v-for="place in places">
      <h3>{{place.name}}</h3>
      <button v-on:click="showPlace(place)">Show address</button>
      <div v-if="currentPlace === place">
        <h3>{{place.address}}</h3>
        <div>
          Name: <input type="text" v-model="place.name">
          Address: <input type="text" v-model="place.address">
          <button v-on:click="updatePlace(place)">Update Place</button>
          <button v-on:click="destroyPlace(place)">Wipe from the face of the Map</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      places: [], 
      currentPlace: {},
      newPlaceName: "", 
      newPlaceAddress: ""
    };
  },
  created: function() {
    axios.get("/api/places").then(response => {
      this.places = response.data;
    });
  },
  methods: {
    createPlace: function() {
      var params = {
        name: this.newPlaceName, 
        address: this.newPlaceAddress
      };
      axios.post("/api/places", params).then(response => {
        this.places.push(response.data);
        this.newPlaceName = "";
        this.newPlaceAddress = "";
      });
    },
    showPlace: function(place) {
      if (this.currentPlace === place) {
        this.currentPlace = {};
      } else {
        this.currentPlace = place;
      }
    }, 
    updatePlace: function(place) {
      var params = {
        name: place.name, 
        address: place.address
      };
      axios
        .patch("/api/places/" + place.id, params)
        .then(response => {
          this.currentPlace = {};
        });
    }, 
    destroyPlace: function(place) {
      axios
        .delete("/api/places/" + place.id)
        .then(response => {
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        });
    }
  }
};
</script>
