<template>
  <v-app>
    <v-app-bar>
      <v-container class="d-flex align-center py-0">
        <v-app-bar-title class="pl-0">
          <div class="d-flex align-center">Ribbon</div>
        </v-app-bar-title>
      </v-container>
    </v-app-bar>

    <v-main>
      <section id="hero">
        <v-sheet class="d-flex align-center pb-16" color="grey-darken-3">
          <v-container class="text-center">
            <v-responsive class="mx-auto">
              <h3 class="text-h3">Try Ribbon's all new features</h3>

              <p class="mt-4 text-medium-emphasis">
                Our all-in-one platform gives you the banking, accounting, fundraising, and organizational tools you need to build a successful charity under the umbrella of your fiscal sponsor.
              </p>
            </v-responsive>
          </v-container>
        </v-sheet>
      </section>

      <v-sheet>
        <section id="filter">
          <v-container>
            <v-row justify="space-between">
              <v-col cols="auto">
                <h2 class="text-h4">Ribbon Donor List</h2>

                <p class="text-primary mt-3">In Beta now!</p>

                <p class="mt-3">See all those that have given in one place!</p>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <DonationTable />
              </v-col>
            </v-row>
          </v-container>
        </section>
      </v-sheet>

      <v-sheet class="py-16" color="#1818181a">
        <section id="grid">
          <v-container>
            <v-row justify="space-between">
              <v-col cols="auto">
                <v-responsive width="350">
                  <h2 class="text-h4">Show your support feature</h2>
                  <p class="text-success mt-3">Available now!</p>
                  <p class="mt-3">Easily send messages to those that have given!</p>
                </v-responsive>
              </v-col>
              <v-sheet width="400" class="mx-auto">
                <MessageForm />
              </v-sheet>
            </v-row>
          </v-container>
        </section>
      </v-sheet>
    </v-main>

    <v-footer>
      <v-container class="text-overline d-flex align-center justify-space-between">
        <div>Copyright &copy; 2023 Flourish Change Inc dba Ribbon</div>

        <v-icon icon="mdi-bank" size="x-large" />
      </v-container>
    </v-footer>
  </v-app>
</template>

<style>
@import './styles/app.scss';
</style>

<script>
import DonationTable from './components/DonationTable.vue';
import MessageForm from './components/MessageForm.vue';

export default {
  name: 'App',
  components: {
    DonationTable,
    MessageForm,
  },
};
</script>
