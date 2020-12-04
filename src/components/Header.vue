<template>
  <v-container>
    <h1 class="mt-4 text-h4 font-weight-bold">
      <v-icon large color="blue">
        mdi-github
      </v-icon>
      {{ title }}
    </h1>
  </v-container>
</template>

<script>
export default {
  name: 'Header',

  props: {
    title: String,
    subtitle: String,
  },
}
</script>
