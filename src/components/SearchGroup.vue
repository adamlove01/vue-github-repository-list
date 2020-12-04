<template>
  <v-container>
    <v-row>
      <v-spacer></v-spacer>
      <v-col cols="12" sm="6">
        <v-combobox
          hide-no-data
          outlined
          clearable
          hide-details
          @keyup.enter="getRepoList($event)"
          placeholder="Enter a Github username..."
        ></v-combobox>
        <p class="subtitle mt-4">
          Please enter a Github user name,<br />
          for example 'andrew' or 'yyx990803' or 'fat'
        </p>

        <p class="mt-4 red--text" v-show="userNotFound">
          User Not Found! Please try again.
        </p>
        <p class="mt-4 red--text" v-show="userHasNoRepos">
          This user has no Repos. Sorry.
        </p>
      </v-col>

      <v-col cols="12" sm="6">
        <v-card
          class="mx-auto"
          outlined
          mt="1"
          v-show="userHasNoRepos || items.length > 0"
        >
          <v-list-item two-line>
            <v-list-item-content>
              <div class="overline mb-4">
                {{ userInfo.name }}
              </div>
              <v-list-item-title class="headline mb-1">
                {{ userInfo.login }}
              </v-list-item-title>
              <a :href="userInfo.url" target="_blank">
                <v-list-item-subtitle>{{
                  userInfo.url
                }}</v-list-item-subtitle></a
              >
            </v-list-item-content>
            <a :href="userInfo.url" target="_blank">
              <v-list-item-avatar tile size="80" color="grey" class="mr-1"
                ><v-img :src="userInfo.avatar" :alt="userInfo.login"></v-img
              ></v-list-item-avatar>
            </a>
          </v-list-item>
        </v-card>
      </v-col>
      <v-spacer></v-spacer>
    </v-row>

    <v-row v-show="items.length > 0" class="mt-6">
      <v-col cols="12" sm="6" md="4">
        <v-combobox
          hide-no-data
          outlined
          clearable
          @keyup.enter="searchTerm($event)"
          placeholder="Search repos..."
        ></v-combobox>
      </v-col>
      <v-col cols="12" sm="6" md="8">
        <v-select
          multiple
          outlined
          clearable
          deletable-chips
          chips
          hide-details
          height="50"
          :items="uniqueTags"
          v-model="tagList"
          placeholder="Language Tags"
        ></v-select>
      </v-col>
    </v-row>

    <v-row v-show="items.length > 0">
      <v-col>
        <h2>Showing {{ filteredCount }} of {{ allCount }} repositories</h2>
        <p class="subtitle">Only shows first 100, sorted by 'latest updated'</p>
      </v-col>
    </v-row>

    <v-row v-show="items.length > 0">
      <Repositories
        :repoItems="filteredList"
        :moreInfo="showInfo"
        @trigger-more-info="handleShowMore"
        @tag-filter-x="filterByTag"
      />
    </v-row>
  </v-container>
</template>

<script>
import Repositories from '@/components/Repositories.vue'

export default {
  name: 'SearchGroup',
  data() {
    return {
      items: [],
      filteredList: [],
      showInfo: [],
      count: 0,
      uniqueTags: [],
      tagList: [],
      userInfo: {},
      userNotFound: false,
      userHasNoRepos: false,
    }
  },
  watch: {
    tagList() {
      // Filter repo based on selected tags after a change
      if (this.tagList.length < 1) {
        this.filteredList = this.items
      } else {
        this.filteredList = this.items.filter((i) => {
          return this.tagList.includes(i.language)
        })
      }
    },
  },
  computed: {
    filteredCount() {
      return this.filteredList.length
    },
    allCount() {
      return this.items.length
    },
  },
  methods: {
    searchTerm(event) {
      // Filter repo by searchTerm on 'name' and 'description' fields
      const searchTerm = event.target.value || ''
      if (searchTerm === '') {
        this.filteredList = this.items
      } else {
        this.filteredList = this.items.filter((i) => {
          if (i.name !== null && i.description !== null) {
            return (
              i.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
              i.description.toLowerCase().includes(searchTerm.toLowerCase())
            )
          }
        })
      }
    },
    // 1. Compile a list of all language tags;
    // 2. Find unique tags from the list;
    // 3. Create an options array from the tags
    getUniqueTags() {
      function onlyUnique(value, index, self) {
        return self.indexOf(value) === index
      }
      const allTags = []
      this.items.map((tag) => {
        allTags.push(tag.language)
      })
      const unique = allTags.filter(onlyUnique)

      const uniqueOptions = []
      unique.map((tag, index) => {
        uniqueOptions.push({
          key: index,
          text: tag,
          value: tag,
        })
      })
      return uniqueOptions
    },
    filterByTag(tag) {
      // Add the clicked tag if not already selected
      if (!this.tagList.includes(tag)) this.tagList.push(tag)
    },
    handleShowMore(i) {
      this.$set(this.showInfo, i, !this.showInfo[i])
    },
    async getUser(username) {
      // Get Github user and pull userInfo from the result
      try {
        const user = await fetch(
          `https://api.github.com/users/${username}`
        ).then((res) => res.json())
        if (user.name === null) user.name = '(no name)'
        this.userInfo = {
          name: user.name,
          login: user.login,
          avatar: user.avatar_url,
          url: user.html_url,
        }
        return true
      } catch (error) {
        this.userNotFound = true
        this.resetList()
        return false
      }
    },
    async getRepoList(event) {
      this.userNotFound = false
      this.userHasNoRepos = false
      // Get repo data from Github
      if (event !== undefined) {
        const username = event.target.value
        const verifiedUser = this.getUser(username)

        if (verifiedUser) {
          try {
            let url = new URL(`https://api.github.com/users/${username}/repos`)
            let params = {
              type: 'public',
              affiliation: 'owner',
              per_page: 100,
              page: 1,
              sort: 'updated',
              direction: 'desc',
            }
            Object.keys(params).forEach((key) =>
              url.searchParams.append(key, params[key])
            )
            const result = await fetch(url).then((res) => res.json())

            // Ensure that all object values are not null | undefined | ''
            result.forEach(function(object) {
              for (let key in object) {
                if (object[key] == null) object[key] = '(none)'
              }
            })

            if (result.length === 0) {
              this.userHasNoRepos = true
              this.resetList()
            } else {
              this.items = result
              this.filteredList = result

              result.forEach(() => {
                this.showInfo.push(false)
              })

              // Regenerate tags
              this.uniqueTags = this.getUniqueTags()
            }
          } catch (error) {
            this.userNotFound = true
            this.resetList()
          }
        }
      }
    },
    resetList() {
      this.items = []
      this.filteredList = []
      this.showInfo = []
      this.userInfo = {}
    },
  },
  created() {
    // Get data from Github -- see getRepoList() function
    this.getRepoList()
  },

  mounted() {
    // Populate the tags dropdown with all unique tags
    this.uniqueTags = this.getUniqueTags()
  },
  components: {
    Repositories,
  },
}
</script>

<style>
.v-text-field.v-text-field--enclosed .v-text-field__details {
  display: none;
}
</style>
