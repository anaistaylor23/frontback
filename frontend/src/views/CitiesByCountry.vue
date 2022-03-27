<template>
  <div class = "titre">
    <h2> Liste des villes par pays</h2>
    <div>
      <label id = "countries" for="countries"></label>
        <select id="countrySelector" @input="fetchCities">
          <option selected disabled hidden>Choisissez un pays</option>
          <option 
            v-for="country in data.allCountries"
            :key = "country.id"
            :value = "country._links.cities.href"> 
            {{country.name}}
          </option>
      </select>
    </div>
    <hr>
    <div>
    <CitiesCountryList v-bind:cities="data.allCities"/>
    </div>
  </div>
</template>

<script setup>
import {reactive, onMounted} from "vue";
import CitiesCountryList from "@/components/CitiesCountryList.vue";
const data = reactive({
  allCountries: [],
  allCities: [],
});
function refresh() {
  fetch("api/countries")
    .then((response) => {
      if (!response.ok) { // status != 2XX
        throw new Error(response.status);
      }
      return response.json();
    })
    .then((json) => {
      data.allCountries = json._embedded.countries;
    })
    .catch((error) => alert(error));
}
function fetchCities() {
   fetch(document.getElementById("countrySelector").value)
    .then((response) => response.json())
    .then((json) => {
      data.allCities = [];
      let listCity = json._embedded.cities;
      for(let i=0; i<listCity.length;i++){
        data.allCities.push(listCity[i]);
      }
    })
    .catch((error) => alert(error));
}
onMounted(() => {
  refresh();
  fetchCities();
});
</script>