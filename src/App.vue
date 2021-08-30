<template>
  <v-app>
      <Main />
    <v-main>
      <v-container fluid class="px-0 py-0">
          <router-view></router-view>
      </v-container>
    </v-main>

      <Footer />
  </v-app>
</template>

<script>
import Main from './components/Main';
import Footer from './components/Footer';

export default {
  name: 'App',

  components: {
    Footer,
    Main,
  },

  data: () => ({
    //
  }),
};
</script>

<style>

</style>
