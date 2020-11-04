<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="crypto logo"
          class="shrink mr-2"
          contain
          src="./img/crypto.png"
          transition="scale-transition"
          width="70"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://en.wikipedia.org/wiki/Cryptocurrency"
        target="_blank"
        text
      >
        <span class="mr-2">CryptoCurrency</span>
      </v-btn>
    </v-app-bar>

    <v-main>
      <HelloWorld />
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>
