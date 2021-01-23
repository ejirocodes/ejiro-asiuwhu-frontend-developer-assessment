<template >
  <v-container class="dashboard">
    <v-card color="grey lighten-4" flat height="200px" tile>
      <v-data-table
        :headers="headers"
        :items="attendees"
        :items-per-page="10"
        :search="search"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <h1>Event Attendees</h1>
            <v-spacer></v-spacer>
            <v-text-field
              v-model="search"
              outlined
              single-line
              dense
              hide-details
              append-icon="mdi-magnify"
              label="Search for attendees..."
              class="ma-2"
            ></v-text-field>
          </v-toolbar>
        </template>
        <template v-slot:[`item.country`]="{ item }">
          <v-btn
            x-small
            elevation="0"
            color="primary"
            @click="guessNationality(item)"
          >
            See Nationality
          </v-btn>
        </template>
        <template v-slot:[`item.gender`]="{ item }">
          <v-chip :color="getColor(item.gender)" outlined x-small>
            {{ item.gender }}
          </v-chip>
        </template>
      </v-data-table>
    </v-card>
    <div class="text-center">
    <v-snackbar
      v-model="noNationality"
      :timeout="timeout"
    >
      {{ text }}

      <template v-slot:action="{ attrs }">
        <v-btn
          color="blue"
          text
          v-bind="attrs"
          @click="noNationality = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
  </v-container>
</template>
<script>
import attendees from "../data/attendees.json";
export default {
  data() {
    return {
      search: "",
      headers: [
        { text: "No", value: "id" },
        {
          text: "Name",
          align: "start",
          sortable: true,
          value: "name",
        },
        { text: "Gender", value: "gender" },
        { text: "Email", value: "email" },
        { text: "Nationality", value: "country" },
      ],
      attendees,
      nationality: null,
      noNationality: false,
      text: '',
      timeout: 2000,
    };
  },

  methods: {
    async guessNationality(item) {
      const res = await fetch(`https://api.nationalize.io?name=${item.name}`);
      const data = await res.json();
      let nationalities = data.country;

      console.log(nationalities);
      if(nationalities.length <= 0) {
        this.noNationality = true;
        this.text = 'Could not user find Nationality!'
      }
      if(nationalities.length && nationalities.length > 0) {
        nationalities.forEach(nat => {
          // let max= a=> a.reduce((m,x)=> m>x ? m:x);
          console.log(nat)
          this.nationality = nat;
        })
      }
    },
    getColor(gender) {
      if (gender === "Male") {
        return "sec";
      } else return "primary";
    },
  },
  computed: {
    colour(gender) {
      let colour;
      if (gender == "Male") {
        colour = "blue";
      } else {
        colour = "red";
      }
      return colour;
    },
  },
};
</script>
<style>
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
</style>