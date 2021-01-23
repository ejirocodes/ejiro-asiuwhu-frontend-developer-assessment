<template >
  <v-container class="dashboard">
    <v-card class="card-table">
      <v-row class="px-4 pt-4 pb-2">
        <v-col cols="12" md="6">
          <h1>Event Attendees</h1>
        </v-col>
        <v-spacer></v-spacer>
        <v-col cols="12" md="6">
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
        </v-col>
      </v-row>
      <v-data-table
        :headers="headers"
        :items="attendees"
        :items-per-page="10"
        :search="search"
      >
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
          <v-chip :color="getColor(item.gender)" outlined small>
            {{ item.gender }}
          </v-chip>
        </template>
      </v-data-table>
      <div class="text-center">
        <v-snackbar v-model="findNationality" :timeout="timeout" color="sec">
          {{ text }}

          <template v-slot:action="{ attrs }">
            <v-btn
              color="#fff"
              text
              v-bind="attrs"
              @click="findNationality = false"
            >
              Close
            </v-btn>
          </template>
        </v-snackbar>
      </div>
    </v-card>
  </v-container>
</template>
<script>
import attendees from "../data/attendees.json";
import countries from "../data/countries";
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
      countries,
      nationality: null,
      findNationality: false,
      text: "",
      timeout: 2500,
    };
  },

  methods: {
    async guessNationality(item) {
      const res = await fetch(`https://api.nationalize.io?name=${item.name}`);
      const data = await res.json();
      let nationalities = data.country;

      if (nationalities.length <= 0) {
        this.findNationality = true;
        return (this.text = `Oops! we could not user find ${item.name} Nationality! 😭`);
      }
      // Get probability of countries and add them to a single array
      const probability = [
        ...new Set(nationalities.map((it) => it.probability)),
      ];
      // Get the country with max probability
      let maxProbality = Math.max(...probability);

      // Find the country from the attendees array based on the highest probability
      const matchedCountry = nationalities.find(
        (nat) => nat.probability === maxProbality
      );

      // Get the country name based on the country ID retured from attendees.json
      let matchedNationName = this.countries.find(
        (country) => matchedCountry.country_id.toLowerCase() === country.alpha2
      );
      this.findNationality = true;
      this.text = `${item.name} is from ${matchedNationName.name}`;
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
.card-table {
   margin: 4rem 0 5rem !important;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.131) !important;
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
</style>