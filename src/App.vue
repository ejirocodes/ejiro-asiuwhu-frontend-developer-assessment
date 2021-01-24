<template>
  <v-app>
    <v-main>
      <Dashboard />
    </v-main>
  </v-app>
</template>

<script>
import Dashboard from "./pages/dashboard";

export default {
  name: "App",

  components: {
    Dashboard,
  },

  data: () => ({
    
  }),
 
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Lato:wght@400;700&display=swap");
.v-application {
  font-family: "Lato", sans-serif !important;
}
.card-table {
  margin: 4rem 0 5rem !important;
    box-shadow: 0 14px 50px -12px rgba(0,0,0,.25) !important;
    border-radius: 12px !important;
    }
.v-btn__content {
  text-transform: none;
  font-size: 11px;
}
.v-btn {
  padding: 15px 25px !important;
}
.text-start {
  padding: 20px 17px !important;
}
.v-data-table > .v-data-table__wrapper > table > tbody > tr > th,
.v-data-table > .v-data-table__wrapper > table > thead > tr > th,
.v-data-table > .v-data-table__wrapper > table > tfoot > tr > th {
  font-size: 1rem !important;
  color: #fd9575 !important;
}
h1 {
  color: #612715;
}
.theme--light.v-data-table
  > .v-data-table__wrapper
  > table
  > tbody
  > tr:hover:not(.v-data-table__expanded__content):not(.v-data-table__empty-wrapper) {
  background: #3fa4b151 !important;
}
.loader {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  z-index: 122;
}
</style>