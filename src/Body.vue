<!--Body is a vue component that is a parent of both 'map' and the data option filter. -->

<template>
  <div>
  <!--Data filtered from the by the 'show all before' drop-down is passed down as a prop of map, to be rendered -->
    <Map v-bind:filteredData="this.filterDataByYear(this.importedData)"></Map>
      <div class="options">
      <label for="select">Show all before:</label>
      <!--Changing the 'show all before' drop-down changes the 'publicationYear' in data-->
      <input class="input"v-model="publicationYear"></input>
      </div>
  </div>
</template>

<script>
import Map from "./Map.vue";
//'reserach data' represents the json data
import researchData from "./assets/data.json";

export default {
  name: "Body",
  data: function () {
    return {
      importedData: researchData,
      //publication year is set to 2020 by default
      publicationYear: 2020,
    };
  },
  components: {
    //map is a child of body
    Map,
  },
  methods: {
    //filterDataByYear allows the json data to be filtered by the 'show all before' date, filtering out any that have dates beofre the stored publication date.
    filterDataByYear: function (data){
      const filterByDate = (data.filter ((dataset) => {
        return this.publicationYear > dataset.temporal.slice(0, 4) 
      }))
      //Two of the datasets 'spatial' coordinates are in a different format to the others. This function filters these two out so that they do not break the code further on. 
      //These coordinates are not rendered on page. Future improvements could have these two data points rendered as markers, rather than polygons.
      return filterByDate.filter((data) => {
        return (typeof data.spatial.coordinates[0]=== 'object')
      })

    }
  }
};
</script>
