<template>
  <div class="hello">

    <b-container class="bv-example-row">
        <b-row>
            <b-col cols="4"></b-col>
            <b-col cols="4"><b-form-select v-model="selected" :options="myMovie" class="mb-3" v-on:change="fetchTable" /></b-col>
            <b-col cols="4"></b-col>
        </b-row>
        <b-row >
            <b-col cols="8"></b-col>
            <b-col cols="4">
              <b-form-select v-model="select" :options="option" class="mb-3" />
              <div><strong>{{ select }}</strong></div>
          </b-col>
        </b-row>
    <div><marquee><strong>{{ selected }}</strong></marquee></div>
    <img src="../assets/loading.gif" v-show="loading">
    <b-table v-show="open" striped hover :items="newCharacter" :fields="fields"></b-table>
    <span v-show="open"  v-model="totalHeight" ><strong>Total height: </strong>{{ totalHeight }} cm ({{feetHeight}} ft)</span>
  
    </b-container>
    <img src="../assets/starwar.jpg" v-show="!open">

  </div>
</template>

<script>
import axios from 'axios'
import Vue from 'vue'
Vue.use('axios')
export default {
  name: 'hello',
  data () {
    return {
      // msg: 'Welcome to Your Vue.js App',
      movies: [],
      open: false,
      loading: false,
      myMovie: [{ value: null, text: 'Please select an option' }],
      movChar: [],
      people: [],
      ind: '',
      selected: null,
      options: [
        { value: null, text: 'Please select an option' },
        { value: 'a', text: 'This is First option' },
        { value: 'b', text: 'Selected Option' },
        { value: {'C': '3PO'}, text: 'This is an option with object value' },
        { value: 'd', text: 'This one is disabled', disabled: true }
      ],

      select: null,
      option: [
        {value: null, text: 'please select gender'},
        {value: 'Male', text: 'male'},
        {value: 'Female', text: 'female'}
      ],
      fields: {
        name: {
          label: 'Name',
          sortable: false
        },
        gender: [
          {
            name: 'Male', sortable: true
          },
          {
            name: 'Female', sortable: true
          }
        ],
        height: {
          label: 'Height',
          sortable: false
        }
      },
      totalFields: {
        Total: {
          label: 'Total',
          sortable: false
        }
      },
      items: []
    }
  },
  created () {
    this.$on('LoadPeople', () => this.fetchPeople())
    this.$on('InsertPeople', () => this.insertPeople())
    this.$on('loadMovie', () => this.movieList())
  },
  methods: {
    message () {
      return 'This is a message'
    },
    fetchMovies () {
      this.loading = true
      axios.get('https://swapi.co/api/films')
      .then(
        (res) => {
          this.movies = res.data
          this.$emit('loadMovie')
        }
      )
      .catch()
    },
    fetchTable () {
      this.open = true
      this.movChar = []
      this.people = []
      this.loading = true
      const id = this.myMovie.indexOf(this.selected)
      const movieCharacter = this.movies.results[id].characters
      movieCharacter.map((character) => {
        this.movChar.push(character)
      })
      this.$emit('LoadPeople')
      this.loading = false
    },
    fetchPeople () {
      this.loading = false
      const peopleLink = this.movChar
      peopleLink.map((link) => {
        axios.get(link)
        .then(
          (res) => {
            this.people.push(res.data)
          })
        .catch()
      })
      this.items = []
      this.$emit('InsertPeople')
      this.loading = false
    },
    insertPeople () {
      this.loading = false
      const people = this.people
      people.map((ppl) => {
        const item = {isActive: true, age: ppl.age, name: ppl.name, gender: ppl.gender, height: ppl.height}
        this.items.push(item)
      })
      this.loading = false
    },
    movieList () {
      this.movies.results.map((movie) => {
        this.myMovie.push(movie.title)
      })
      this.loading = false
    }
  },
  mounted () {
    this.fetchMovies()
  },
  computed: {
    totalHeight: function () {
      let b = []
      this.newCharacter.map((ppl) => {
        let a = parseInt(ppl.height)
        b.push(a)
      })
      var count = 0
      for (var i = 0; i < b.length; i++) {
        count += b[i]
      }
      return count
    },
    feetHeight: function () {
      return this.totalHeight / 30
    },
    selectedValue: function () {
      this.ind = this.myMovie.indexOf(this.selected)
    },
    newCharacter: function () {
      const ppl = this.people
      const pplArr = []
      ppl.map((character) => {
        const myppl = {}
        myppl.name = character.name
        myppl.height = character.height
        myppl.gender = character.gender
        pplArr.push(myppl)
      })
      return pplArr
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}


</style>

