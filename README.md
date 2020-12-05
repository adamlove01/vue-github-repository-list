# Github Repo List

This app allows you to list all repos of any Github user by user name. Within the resulting list u you can search the repo name and description \and also filter by programming language using tags.

## App Views

### Home.vue

This is the default view and currently the only page route for the app.

## App Components

### Header.vue

This displays the title and subtitle of the app.

### SearchGroup.vue

This component has a search box and retrieves the repository list from Github and displays the data in Repositories.vue. It also shows a card with the user's name and avavtar.

### Repositories.vue

This component displays the information for each repository.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Installing

Clone the project and cd to its folder.

```
npm install
```

Start the development server

```
npm run serve
```

Go to http://localhost:8080 to view the Home page.

## Vue CLI

This project was bootstrapped with [Vue CLI](https://github.com/vuejs/vue-cli). You can find more information on how to perform common tasks [here](https://cli.vuejs.org/).

## Built With

- [Vue](https://github.com/vuejs/vue)
- [Vue Router](https://github.com/vuejs/vue-router)
- [Vuetify](https://github.com/vuetifyjs/vuetify)
- [Node/npm](https://github.com/nodejs/node)

## Author

- **Adam Love**

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
