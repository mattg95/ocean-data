<template>
  <div class="mapandbar">
    <div class="map" id="map"></div>
    <!--'Sidebar' is passed two props:'clickedId' is the id of the polygon which has been clicked. 
    'ImportedData is the json data, which has been filtered and sorted in the 'Body' and 'Map'-->
    <Sidebar
      class="sidebar"
      :clickedId="this.clickedId"
      :importedData="this.sortDataByAreas(this.filteredData)"
    ></Sidebar>
  </div>
</template>

<script>
import gmapsInit from "./utils/gmap.js";
import Sidebar from "./Sidebar.vue";
//'computeArea' is a plug-in which is imported to calculate the area of each polygon,
import { computeArea } from "spherical-geometry-js";

export default {
  name: "Map",
  data: () => {
    return {
      //'clickedId' is set to a negative number by default. When a polygon is clicked, it will change to a number between 0 and 49
      clickedId: -1,
    };
  },
  //'Sidebar' is a child of map
  components: { Sidebar },
  //'filteredData' is the json data that was passed to 'body' and filtered according to the chosen date.
  //Unless a date has been entered, 'filteredData' will contain all the json data.
  props: { filteredData: Array },
  methods: {
    //'createMap' loads the google map and renders data on it
    createMap: async function(geoData) {
      try {
        //'vm' ensure that the vue component is still in scope as 'this'
        const vm = this;
        const google = await gmapsInit();
        //'WorldMap' is the base map on which data is rendered
        const worldMap = new google.maps.Map(document.getElementById("map"));
        worldMap.setCenter(new google.maps.LatLng(0, 0));
        worldMap.setZoom(2);

        //this function renders each dataset as a 'feature' on the base map
        await geoData.map((dataset, i) => {
          worldMap.data.addGeoJson({
            type: "Feature",
            //geometry defines the polygon shape on the map
            geometry: dataset.spatial,
            properties: {
              id: i,
              //colour is a randomly generated hex number, to allow for visual differentiaion between the polygons.
              //This functionionality is defined outside of the 'set style' func, to avoid the colour changing on mouse events such as mousover and clisk
              colour:
                "#" +
                Math.random()
                  .toString(16)
                  .slice(2, 8),
              fillOpacity: 0.5,
              zIndex: dataset.zIndex,
            },
          });
        });
        //this defined the colour and opacity of each feature
        worldMap.data.setStyle((data) => {
          return {
            fillColor: data.i.colour,
            fillOpacity: 0.5,
          };
        });
        //this gives each feature a higher opacity count on mouse hover, so that the user can see which polygon they will be clicking on
        worldMap.data.addListener("mouseover", function(event) {
          worldMap.data.overrideStyle(event.feature, {
            fillOpacity: 0.7,
          });
        });
        //This reverts the opacity style when the mouse leaves the polygon space
        worldMap.data.addListener("mouseout", function(event) {
          worldMap.data.revertStyle(event.feature);
        });
        //On click, the vue component data 'clickedId' will be set to the id of the clicked feature\s
        worldMap.data.addListener("click", function(event) {
          const eventId = event.feature.getProperty("id");
          vm.clickedId = eventId;
        });
      } catch (error) {
        console.error(error);
      }
    },
    //'sortDataByAreas' uses the 'computeArea' plug in to compute the area of each polygon. It then orders each polygon according to their size.
    //When the Polygons are rendered, the smallest ones are rendered with a higher z-index, ensuring that the small polygons are not obscured by larger ones on the map.

    sortDataByAreas: function(data) {
      const dataWithAreas = data.map(function(dataset) {
        dataset.area = computeArea(dataset.spatial.coordinates[0]);
        return dataset;
      });
      return dataWithAreas.sort(function(a, b) {
        return b.area - a.area;
      });
    },
  },

  //on mounting, the createMap() method is invoked with the json data passed down from body
  mounted() {
    console.log("mounting...");
    const invokeStartData = this.sortDataByAreas(this.filteredData);
    this.createMap(invokeStartData);
  },
  //this func watches for changes in the filteredData (if a user has made changes to the 'show all before date' drop down in 'body', the filteredData will change)
  //this ensures that the data is re-rendered every time the user changes the date
  watch: {
    filteredData: {
      // the callback will be called immediately after the start of the observation
      immediate: true,
      //if filteredData changes, the createMap()method is called with the new filtered data
      handler(filteredGeoData) {
        this.createMap(this.sortDataByAreas(filteredGeoData));
      },
    },
  },
};
</script>
