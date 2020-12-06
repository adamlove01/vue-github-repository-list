<template>
  <v-container style="max-width: 1200px">
    <Header title="Github Repo List" />
    <SearchGroup />
  </v-container>
</template>

<script>
import Header from '@/components/Header.vue'
import SearchGroup from '@/components/SearchGroup.vue'

export default {
  name: 'home',

  components: {
    Header,
    SearchGroup,
  },
}
</script>
