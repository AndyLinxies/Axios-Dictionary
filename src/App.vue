<template>
  <div id="app">
    <p class="text-3xl mb-4 font-extrabold"> Dictionary</p>

    <div class="flex justify-center">
    <button
          type="submit"
          class="mt-4 px-4 py-2 font-medium tracking-wide text-white capitalize transition-colors duration-200 transform bg-green-600 rounded-md hover:bg-blue-500 focus:outline-none focus:ring focus:ring-blue-300 focus:ring-opacity-80"
          @click="handleLanguage('fr')"
        >
          Français
        </button>
    <button
          type="submit"
          class="mt-4 px-4 py-2 font-medium tracking-wide text-white capitalize transition-colors duration-200 transform bg-purple-600 rounded-md hover:bg-blue-500 focus:outline-none focus:ring focus:ring-blue-300 focus:ring-opacity-80 ml-10"
          @click="handleLanguage('en')"
        >
          English
        </button>

    </div>

    <div
      class="max-w-2xl px-8 py-4 mx-auto bg-white rounded-lg shadow-xl dark:bg-gray-800 mt-16"
    >
      <form action="" @submit="launch">
        <label v-if="language=='en'" class="text-2xl font-bold" for="word">Type a word to get its meaning</label>
        <label v-if="language=='fr'" class="text-2xl font-bold" for="word">Tapez un mot pour obtenir sa signification</label>

        <input
          id="word"
          type="text"
          class="block w-full px-4 py-2 mt-2 text-gray-700 bg-white border border-gray-300 rounded-md dark:bg-gray-800 dark:text-gray-300 dark:border-gray-600 focus:border-blue-500 dark:focus:border-blue-500 focus:outline-none focus:ring"
          v-model="wordFromInput"
        />
      <p class="text-red-500">{{message}}</p>
      <p :class="notFoundMessage? 'block text-center':'hidden' " class="text-red-500">{{notFoundMessage}}</p>
        <button
          type="submit"
          class="mt-4 px-4 py-2 font-medium tracking-wide text-white capitalize transition-colors duration-200 transform bg-blue-600 rounded-md hover:bg-blue-500 focus:outline-none focus:ring focus:ring-blue-300 focus:ring-opacity-80"
        >
          Go
        </button>
      </form>
    </div>
    <div class="text-center mt-5">
      <!-- <p v-for="theMeaning in meaning.origin" :key="theMeaning">Origin {{theMeaning}}</p> -->
      <p class="mb-5">Language: <i>{{language}}</i></p>
      
      <p :class="origin?'block text-center':'hidden' " class=" ml-10 mb-5 text-blue-500"><b> Origin:</b> <i>{{origin}}</i> </p>

      <p><b>Definitions</b> </p>
      <p class="text-green-500 font-bold mt-4"  v-for="realMeaning,i in realMeanings" :key="i">* {{realMeaning.definition}} <span :class="realMeaning.example ?'block text-center':'hidden' " class="text-black text-sm">Example: {{realMeaning.example}}</span></p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: "App",
  components: {},
  data() {
    return {
      wordFromInput:'',
      meaningResult:'',
      origin:'',
      realMeanings:'',
      message:'',
      notFoundMessage:'',
      status:'',
      language:'en'
    }
  },
  methods: {
    launch(e){
              e.preventDefault();

      if (this.wordFromInput) {
        
        e.preventDefault();
          
        axios
          .get( `https://api.dictionaryapi.dev/api/v2/entries/${this.language}/${this.wordFromInput}`)
          .then(response => {
            this.meaningResult = response.data;
            this.origin=this.meaningResult[0].origin;
            this.realMeanings=this.meaningResult[0].meanings[0].definitions
            this.notFoundMessage=""
          } )
          .catch(error => {
            if (error.response) {
              //Pour avoir le status de l'ereur afin de pouvoir personaliser le message d'erreur plus bas
              this.status=error.response.status
            }
          //Message de requete non trouvée
          if (this.status==404) {
            this.notFoundMessage="Word not found Please type a valid one"
          }
          })
          //message pour prevenir de taper qqch dans le input
          this.message=""
      }else{
        e.preventDefault();
        this.message="Please enter something"
      }
      
    },
    handleLanguage(value){
      if (value=='fr') {
        this.language="fr"
        
      }else{
        this.language="en"
      }
    }
  },
  mounted() {
    this.launch
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
