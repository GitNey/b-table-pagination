<template>
  <div id="app">
    <b-container>
      <b-row>
        <b-col align="center">
          <img alt="Vue logo" src="./assets/logo.png">

          <b-alert :show="true" variant="success">
            <p class="m-0">We have bootstrap-vue</p>
          </b-alert>
          <HelloWorld msg="Welcome to Your Vue.js App"/>
        </b-col>
      </b-row>
      <b-row>
        <b-col>

          <!-- ... -->
          <b-form-group>
            <label>Pagination</label>
            <b-select
              v-model="perPage"
              :options="[1, 5, 10, 15, 20, 25, 100]"
            />
          </b-form-group>
          <b-table
            show-empty
            :busy="isBusy"
            :items="items"
            :fields="fields"
            :current-page="currentPage"
            :per-page="0"
          >
            <template v-slot:table-busy>
              <div class="text-center text-danger my-2">
                <b-spinner class="align-middle"></b-spinner>
                <strong>Loading {{ currentPage }} page, please chill...</strong>
              </div>
            </template>
          </b-table>
          <b-pagination
            :total-rows="totalRows"
            v-model="currentPage"
            :per-page="perPage"
          />

        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

const API_URI = 'https://jsonplaceholder.typicode.com/'

export default {
  name: 'App',

  data () {
    return {
      items: [],
      fields: [
        { key: 'postId', label: 'Post ID' },
        { key: 'id', label: 'ID' },
        { key: 'name', label: 'Name' },
        { key: 'email', label: 'Email' },
        { key: 'body', label: 'Body' }
      ],
      isBusy: true,
      currentPage: 1,
      perPage: 10,
      totalRows: 0
    }
  },

  components: {
    HelloWorld
  },

  methods: {
    async fetchCommentData () {
      var vm = this
      let url = `${API_URI}comments/?_page=${this.currentPage}&_limit=${this.perPage}`
      vm.isBusy = true
      this.items = await fetch(url)
        .then(function(response) {
          if (response.headers.get('x-total-count')) {
            console.log('The "x-total-count" response header is present in the response...')
          }
          vm.totalRows = parseInt(response.headers.get('x-total-count'))
          return response.json()
        })
        .then(items => {
          vm.isBusy = false
          return items
        })
    }
  },
  watch: {
    currentPage () {
      this.fetchCommentData()
        .catch(error => console.error(error))
    },
    perPage () {
      this.fetchCommentData()
        .catch(error => console.error(error))
    }
  },
  created () {
    this.fetchCommentData()
      .catch(error => console.error(error))
  }
}
</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */
</style>
