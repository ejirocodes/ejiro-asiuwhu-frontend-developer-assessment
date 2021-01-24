<template >
  <v-container class="dashboard">
    <PreLoader v-if="isLoading" />

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
        <v-snackbar
          v-model="findNationality"
          :timeout="timeout"
          :color="color"
          top
          right
        >
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
import PreLoader from "../components/PreLoader";

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
      timeout: 3500,
      color: "sec",
      isLoading: true,
    };
  },
  components: {
    PreLoader,
  },
  methods: {
    async guessNationality(item) {
      try {
        const res = await fetch(`https://api.nationalize.io?name=${item.name}`);
        const data = await res.json();
        let nationalities = data.country;

        // If the country array is empty, notify the user (vendor)
        if (nationalities.length <= 0) {
          this.color = "error";
          this.findNationality = true;
          this.text = `Oops! we could not user find ${item.name} Nationality! 😭`;
          return false;
        }

        // Get probability of countries and add them to a single array
        const probability = [
          ...new Set(nationalities.map((it) => it.probability)),
        ];
        // Get the country with higest probability
        let maxProbality = Math.max(...probability);

        // Find the country from the attendees array based on the highest probability
        const matchedCountry = nationalities.find(
          (nat) => nat.probability === maxProbality
        );

        // Get the country name based on the country ID retured from attendees.json
        let matchedNationName = this.countries.find(
          (country) =>
            matchedCountry.country_id.toLowerCase() === country.alpha2
        );

        // If all criteria are met,
        // show toast with the prdicted user Nationality
        this.findNationality = true;
        this.color = "sec";
        this.text = `${item.name} is from ${matchedNationName.name}`;
      } catch (error) {
        this.findNationality = true;
        this.text = `Something went wrong! Please try again 🙏🏾`;
        this.color = "error";
      }
    },
    getColor(gender) {
      if (gender === "Male") {
        return "sec";
      } else return "primary";
    },
  },
  beforeCreate() {
    this.isLoading = true;
  },
  mounted() {
    setTimeout(() => {
      this.isLoading = false;
    }, 1400);
  },
};
</script>
<style>
</style>