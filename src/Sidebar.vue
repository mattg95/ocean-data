<!--sidebar loads up data on the clicked map polygon, to the side of the map-->
<template>
  <div id="Sidebar">
    <!-- '&&' statements ensure that 'undefined' is never rendered -->
    <h3>
      {{
        importedData[clickedId]
          ? importedData[clickedId].title
          : "Click an area on the map"
      }}
    </h3>
    <p>
      {{
        importedData[clickedId]
          ? importedData[clickedId].description
          : "Coloured areas on the map represnet areas where scientific studies of the ocean have taken place. Please click on a coloured area to dislay further details about that study."
      }}
    </p>
    <p>
      {{ importedData[clickedId] && importedData[clickedId].publisher_name }}
    </p>
  </div>
</template>

<script>
export default {
  name: "SideBar",
  //'clickedId represents the id of the clicked polygon, and is used to render the correct dataset.
  //'imported Data' is the json data, that has been filtered and sorted in the body and map components
  props: { clickedId: Number, importedData: Array },
};
</script>
