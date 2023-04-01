<template>
  <v-data-table :options.sync="options" :server-items-length="total_donors" :loading="loading" :headers="headers" :items="donors.data" :items-per-page="10" class="elevation-1 ribbon-table">
    <template v-slot:top>
      <v-text-field v-model="search" label="Search by name or email" class="mx-4 pt-4" outlined clearable prepend-inner-icon="mdi-magnify"></v-text-field>
      <v-alert v-if="error" type="error" class="mx-4 pt-4">{{ error }}</v-alert>
    </template>
    <template v-slot:[`item.first_donation`]="{ item }">
      {{ new Date(item.first_donation).toLocaleDateString() }}
    </template>
    <template v-slot:progress>
      <v-progress-linear color="#00754A" indeterminate></v-progress-linear>
    </template>
  </v-data-table>
</template>

<script>
import axios from 'axios';
export default {
  name: 'DonationTable',
  data() {
    return {
      headers: [
        { text: 'NAME', value: 'full_name' },
        { text: 'EMAIL', value: 'email' },
        {
          text: 'TOTAL DONATIONS',
          value: 'total_donations',
          filter: (value) => {
            if (!this.total_donations) return true;

            return value > parseInt(this.total_donations);
          },
        },
        { text: 'FIRST DONATION', value: 'first_donation' },
      ],
      total_donors: 0,
      search: '',
      total_donations: '',
      donors: [],
      loading: false,
      available_sorting: [],
      error: '',
      options: {
        page: 1,
        itemsPerPage: 10,
        sortBy: ['total_donations'],
      },
    };
  },
  watch: {
    options: {
      handler() {
        this.getDonors();
      },
      deep: true,
    },
    search: {
      handler() {
        this.getDonors();
      },
      deep: true,
    },
  },
  methods: {
    getDonors() {
      const { page, itemsPerPage, sortBy, sortDesc } = this.options;
      this.error = '';
      this.loading = true;
      axios
        .get(`https://interview.ribbon.giving/api/donors?page=${page}&itemsPerPage=${itemsPerPage}&search=${this.search}&sort_by=${sortBy[0]}&sort_direction=${sortDesc[0] ? 'desc' : 'asc'}`)
        .then((response) => {
          this.donors = response.data;
          this.total_donors = response.data.meta.total;
          this.available_sorting = response.data.filtering.available_sorting;
          this.loading = false;
        })
        .catch((e) => {
          console.log('error', e);
          this.error = e.message || 'There was an error fetching your data, please try again.';
          this.loading = false;
        });
    },
  },
};
</script>
