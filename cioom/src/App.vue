<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <h1>Cards In Opposition Of Mankind</h1>
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >

      </v-btn>
    </v-app-bar>

    <v-main>
      <GamePage
        @data="printData"
      />
    </v-main>
  </v-app>
</template>

<script>
import GamePage from './components/GamePage.vue';

export default {
  name: 'App',

  components: {
    GamePage,
  },
  methods: {
    printData(data) {
      console.log("main",data);
    },
  },

};
</script>
