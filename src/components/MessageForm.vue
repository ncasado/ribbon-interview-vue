<template>
  <v-sheet width="400" class="mx-auto">
    <v-form class="mx-4 my-4" v-model="valid" validate-on="submit" @submit.prevent="submit">
      <v-textarea v-model="message" :rules="messageRules" label="Message" validate-on-blur></v-textarea>
      <v-autocomplete
        v-model="email"
        :loading="loading"
        :items="donorEmails"
        :search-input.sync="search"
        cache-items
        flat
        hide-no-data
        hide-details
        label="Email"
        :rules="emailRules"
        class="my-2 py-2"
        validate-on-blur
      ></v-autocomplete>
      <v-text-field v-model="donor_id" label="Donor Id" readonly :loading="loadingDonorId"></v-text-field>
      <v-btn type="submit" block class="mt-2 mb-4" :loading="submitting">Send</v-btn>
      <v-alert v-if="error" type="error" class="pt-4">{{ error }}</v-alert>
      <v-alert v-if="success" type="success" class="pt-4">Your message was sucessfully sent.</v-alert>
    </v-form>
  </v-sheet>
</template>

<script>
import axios from 'axios';

export default {
  name: 'MessageForm',

  data() {
    return {
      valid: false,
      success: false,
      submitting: false,
      loading: false,
      loadingDonorId: false,
      email: null,
      donor_id: '',
      message: '',
      search: null,
      donorEmails: [],
      error: '',
      emailRules: [
        (value) => {
          if (value) return true;
          this.valid = false;
          return 'E-mail is required.';
        },
      ],
      messageRules: [
        (value) => {
          if (value?.length > 15) return true;
          this.valid = false;
          return 'Message must be at least 15 characters.';
        },
      ],
    };
  },
  watch: {
    search: {
      handler() {
        this.getDonors();
      },
      deep: true,
    },
    email: {
      handler() {
        this.getDonorId();
      },
    },
  },
  methods: {
    getDonors() {
      this.error = '';
      this.loading = true;
      axios
        .get(`https://interview.ribbon.giving/api/donors?search=${this.search}`)
        .then((response) => {
          this.donorEmails = response.data.data.map((d) => d.email);
          this.loading = false;
        })
        .catch((e) => {
          console.log('error', e);
          this.error = e.message || 'There was an error fetching your data, please try again.';
          this.loading = false;
        });
    },
    getDonorId() {
      if (this.email) {
        this.error = '';
        this.loadingDonorId = true;
        axios
          .get(`https://interview.ribbon.giving/api/donors?search=${this.email}`)
          .then((response) => {
            this.donor_id = response.data.data[0].id;
            this.loadingDonorId = false;
          })
          .catch((e) => {
            console.log('error', e);
            this.error = e.message || 'There was an error fetching your data, please try again.';
            this.loading = false;
          });
      }
    },
    async submit() {
      // Send message to server.
      this.success = false;
      this.error = '';
      if (this.valid) {
        const form = {
          email: this.email,
          donor_id: this.donor_id,
          message: this.message,
        };
        const headers = {
          'Content-type': 'application/json',
        };

        try {
          this.submitting = true;
          const response = await axios.post(`https://interview.ribbon.giving/api/donors/${this.donor_id}/send-message`, form, { headers });
          this.success = response.data.Success;
          console.log('response', response.data);
          if (this.success) {
            this.email = null;
            this.donor_id = '';
            this.message = '';
          } else {
            this.error = response.data.message || 'There was an issue while submitting the form, please try again';
          }
          this.submitting = false;
        } catch (e) {
          this.error = e.message || 'There was an issue while submitting the form, please try again';
          this.submitting = false;
          console.log(e);
        }
      }
    },
  },
};
</script>
