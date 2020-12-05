<template>
  <v-container>
    <v-divider />
    <v-row v-for="(item, index) in repoItems" :key="item.id">
      <v-col cols="12" md="4">
        <h3>{{ item.name }}</h3>
      </v-col>

      <v-col cols="12" md="6">
        <p class="">{{ item.description }}</p>
        <a :href="item.website">
          <v-btn class="mr-2 mb-2">
            <v-icon class="mr-2">
              mdi-earth
            </v-icon>
            Website
          </v-btn>
        </a>

        <a :href="item.repository">
          <v-btn class="mr-2 mb-2 white--text" color="#5cbbf6">
            <v-icon class="mr-2">
              mdi-github
            </v-icon>
            Repo
          </v-btn>
        </a>

        <a :href="`${item.repository}/fork`">
          <v-btn class="mr-2 mb-2 white--text" color="#666">
            <v-icon class="mr-2">
              mdi-source-fork
            </v-icon>
            Fork
          </v-btn>
        </a>
      </v-col>

      <v-col cols="12" md="2">
        <a class="ui tag label" @click="tagFilter(item.language)">
          <v-chip class="mr-2 mb-2" color="#2196f3" outlined>
            <v-icon class="mr-2">
              mdi-label
            </v-icon>
            {{ item.language }}
          </v-chip>
        </a>
      </v-col>

      <v-col cols="12">
        <p @click="triggerMoreInfo(index)" class="more-info pa-2 text-center">
          MORE INFO
        </p>
        <transition name="slide">
          <div v-show="item.showMore">
            <v-row>
              <v-col cols="12" md="4">
                <p>Updated at: {{ niceDate(item.updated_at) }}</p>
                <p>Created at: {{ niceDate(item.created_at) }}</p>
              </v-col>
              <v-col cols="12" md="5">
                <p>License: {{ item.license.name }}</p>
                <p>Forks: {{ item.forks_count }}</p>
              </v-col>
              <v-col cols="12" md="3">
                <p>
                  <v-icon color="blue">mdi-star-outline</v-icon> Stars:
                  {{ item.stargazers_count }}
                </p>
                <p>
                  <v-icon color="blue">mdi-eye-outline</v-icon> Watchers:
                  {{ item.watchers_count }}
                </p>
              </v-col>
            </v-row>
          </div>
        </transition>
        <v-divider />
      </v-col>
    </v-row>
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
    triggerMoreInfo(i) {
      this.$emit('trigger-more-info', i)
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
.more-info {
  background-color: #efefef;
  cursor: pointer;
}
.slide-enter-active {
  -moz-transition-duration: 0.3s;
  -webkit-transition-duration: 0.3s;
  -o-transition-duration: 0.3s;
  transition-duration: 0.3s;
  -moz-transition-timing-function: ease-in;
  -webkit-transition-timing-function: ease-in;
  -o-transition-timing-function: ease-in;
  transition-timing-function: ease-in;
}

.slide-leave-active {
  -moz-transition-duration: 0.3s;
  -webkit-transition-duration: 0.3s;
  -o-transition-duration: 0.3s;
  transition-duration: 0.3s;
  -moz-transition-timing-function: cubic-bezier(0, 1, 0.5, 1);
  -webkit-transition-timing-function: cubic-bezier(0, 1, 0.5, 1);
  -o-transition-timing-function: cubic-bezier(0, 1, 0.5, 1);
  transition-timing-function: cubic-bezier(0, 1, 0.5, 1);
}

.slide-enter-to,
.slide-leave {
  max-height: 100px;
  overflow: hidden;
}

.slide-enter,
.slide-leave-to {
  overflow: hidden;
  max-height: 0;
}
</style>
