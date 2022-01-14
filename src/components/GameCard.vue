<template>
  <div class="card-custom">
    <h1 class="text-center mb-5">Capital City Game</h1>
    <div v-if="!gameBegan" class="text-center">
      <p>Welcome to the capital city game where you are given a country and it's flag and you must guess the capital city. See how many you can get out of 10!</p>
      <b-button @click="beginGame()">Begin Game</b-button>
    </div>
    <div v-if="loaded && gameBegan" class="text-center">
      <h5>Your Score: {{ score }}</h5>
      <img :src="randomCountry.flags.png" alt="">
      <h3 class="m-4">
        {{ randomCountry.name.common }}
      </h3>
      <div class="d-flex" v-if="!checkedAnswer">
        <b-form-input v-model="text" v-on:keyup.enter="checkAnswer(false)" placeholder="Enter the capital city"></b-form-input>
        <b-button @click="checkAnswer(false)" class="ml-3" variant="success">Enter</b-button>
        <b-button @click="checkAnswer(true)" class="ml-3" variant="success">Pass</b-button>
      </div>
      <div v-if="checkedAnswer">
        <h5>
          {{ answer }}
        </h5>
        <b-button v-if="numberOfAnswers < 10" class="mt-3" variant="danger" @click="randomiseCountry(tenCountries)">Next country</b-button>
      </div>
    </div>
    <div v-if="numberOfAnswers == 10" class="text-center">
      <p>Thanks for playing! You scored {{ score }}/10</p>
      <b-button variant="success" @click="restartGame()">Play again</b-button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'GameCard',
  props: {
    msg: String
  },
  data() {
    return {
      loaded: false,
      allCountries: [],
      randomCountry: null,
      gameBegan: false,
      text: null,
      checkedAnswer: false,
      answer: null,
      score: 0,
      numberOfAnswers: 0,
      tenCountries: []
    }
  },
  methods: {
    restartGame() {
      this.gameBegan = false
      this.score = 0
      this.numberOfAnswers = 0,
      this.checkedAnswer = false
    },
    randomiseCountry(tenCountries) {
      this.text = ""
      this.checkedAnswer = false
      this.randomCountry = tenCountries[this.numberOfAnswers]
    },
    beginGame() {   
      this.tenCountries = this.allCountries.sort(() => Math.random() - Math.random()).slice(0, 10)
      console.log(this.tenCountries)
      this.randomiseCountry(this.tenCountries)
      this.gameBegan = true
    },
    removeSmallerNations(countryData) {
      let data = []

      countryData.forEach(country => {
        if(country.population > 1000000) {
          data.push(country)
        }
      })

      return data
    },
    checkAnswer(isPass) {
      this.numberOfAnswers += 1
      let userAnswer = this.text.toLowerCase().trim().normalize("NFD").replace(/[\u0300-\u036f]/g, "")
      let capitalAnswer = this.randomCountry.capital.toString().toLowerCase().normalize("NFD").replace(/[\u0300-\u036f]/g, "")
      let answer = userAnswer == capitalAnswer ? true : false
      
      if (answer) {
        this.answer = `Correct! The capital of ${this.randomCountry.name.common} is ${this.randomCountry.capital}`
        this.score += 1
      } else if(isPass) {
        this.answer = `The capital of ${this.randomCountry.name.common} is ${this.randomCountry.capital}`
      } else {
        this.answer = `Nope! The capital of ${this.randomCountry.name.common} is ${this.randomCountry.capital}`
      }
      this.checkedAnswer = true
    }
  },
  async mounted() {
    this.loaded = false
    this.gameBegan = false
    axios.get('https://restcountries.com/v3.1/all')
      .then(
        response => {
          this.allCountries = this.removeSmallerNations(response.data)
          this.loaded = true
        },
        error => console.log((error.response && error.response.data) || error.message || error.toString())
      )
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .card-custom {
    width: 95%;
    padding: 20px;
    background-color: rgb(245, 236, 236);
    border-radius: 5px;
    box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
    @media screen and (min-width: 568px) {
      width: 50%; 
    }
  }
</style>
