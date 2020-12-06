<template>
  <v-container>
    <v-expansion-panels accordion>
      <v-expansion-panel v-for="item in repoItems" :key="item.id">
        <v-expansion-panel-header>
          <v-row>
            <v-col cols="12" md="4">
              <h3 class="h6 pb-2">{{ item.name }}</h3>
              <p class="body-1 mb-0">{{ item.description }}</p>
              <a
                :href="item.homepage"
                target="_blank"
                v-show="item.homepage !== '' && item.homepage !== '(none)'"
              >
                <p class="mt-2 mb-0">Home Page</p>
              </a>
            </v-col>

            <v-col cols="12" md="5">
              <a :href="item.html_url" target="_blank">
                <v-btn class="mr-2" color="white">
                  <v-icon class="mr-2">
                    mdi-github
                  </v-icon>
                  Repo
                </v-btn>
              </a>

              <a
                :href="`${item.html_url}/network/members`"
                target="_blank"
                v-show="item.fork"
              >
                <v-btn class="mr-2" color="white">
                  <v-icon class="mr-2">
                    mdi-source-fork
                  </v-icon>
                  Forks
                </v-btn>
              </a>
            </v-col>

            <v-col cols="12" md="3">
              <a class="ui tag label" @click="tagFilter(item.language)">
                <v-btn
                  class="pl-0 pl-md-4 mb-0"
                  color="#2196f3"
                  outlined
                  style="border-color: white"
                >
                  <v-icon class="mr-2">
                    mdi-label
                  </v-icon>
                  {{ item.language }}
                </v-btn>
              </a>
            </v-col>
          </v-row>
        </v-expansion-panel-header>

        <v-expansion-panel-content>
          <v-row>
            <v-col cols="12" md="4">
              <p class="mb-1">Updated at: {{ niceDate(item.updated_at) }}</p>
              <p class="mb-0">Created at: {{ niceDate(item.created_at) }}</p>
            </v-col>
            <v-col cols="12" md="5">
              <p class="mb-1">License: {{ item.license.name }}</p>
              <p class="mb-0">Forks: {{ item.forks_count }}</p>
            </v-col>
            <v-col cols="12" md="3">
              <p class="mb-1">
                <v-icon color="blue">mdi-star-outline</v-icon> Stars:
                {{ item.stargazers_count }}
              </p>
              <p class="mb-0">
                <v-icon color="blue">mdi-eye-outline</v-icon> Watchers:
                {{ item.watchers_count }}
              </p>
            </v-col>
          </v-row>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>
  </v-container>
</template>

<script>
export default {
  name: 'Repositories',
  props: {
    repoItems: Array,
  },
  methods: {
    tagFilter(tag) {
      this.$emit('tag-filter-x', tag)
    },
    niceDate(date) {
      const d = new Date(date)
      return d.toDateString()
    },
  },
}
</script>

<style>
a {
  text-decoration: none;
}
h3 {
  line-height: 1.4rem;
}
</style>
