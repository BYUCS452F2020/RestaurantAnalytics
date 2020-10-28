<template>
  <v-app>
    <v-main>
      <Restaurant/>
    </v-main>
  </v-app>
</template>

<script>
import Restaurant from './components/Restaurant';

export default {
  name: 'App',

  components: {
    Restaurant,
  },

  data: () => ({
    //
  }),
};
</script>
