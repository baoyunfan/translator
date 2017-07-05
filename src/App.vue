<template>
  <div class="container" id="app">
    <h1 class="text-primary">Word Translator</h1>
    <h5 class="text-muted">powered by <a href="https://vuejs.org/">Vue.js</a> and <a href="http://translate.yandex.com/">Yandex.Translate</a></h5>
    <div style="margin-top: 100px"></div>
    <TranslateForm v-bind:apiKey="apiKey" @translate="translateText"></TranslateForm>
    <TranslateOutput v-text="translatedText"></TranslateOutput>
  </div>
</template>

<script>
import TranslateForm from './components/TranslateForm'
import TranslateOutput from './components/TranslateOutput'

export default {
  name: 'app',
  components: {
    TranslateForm,
    TranslateOutput
  },
  data () {
    return {
      translatedText: '',
      token: '',
      apiKey: 'trnsl.1.1.20170704T221412Z.a1d340f9a09ed17a.87b30cd4faa716ad17416ddef8810b35637ca34a'
    }
  },

  methods: {
    getTokenMS: function () {
      const appSecret = 'ce133f88eeec479083180617027f8654'
      const tokenServiceUrl = 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
      this.$http.post(tokenServiceUrl, '', {headers: {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': appSecret}})
      .then(response => {
        this.token = response.body
      }, response => {
        console.log('Failed to obtain app token')
      })
    },

    translateTextMS: function (textToTranslate, language) {
      let appid = 'Bearer ' + this.token
      let url = 'https://api.microsofttranslator.com/v2/Http.svc/Translate?appid=' + appid + '&text=' + textToTranslate + '&to=' + language
      this.$http.get(url)
      .then(response => {
        let parser = new DOMParser()
        let result = parser.parseFromString(response.body, 'application/xml')
        this.translatedText = result.getElementsByTagName('string')[0].childNodes[0].nodeValue
      }, response => {
        console.log('Failed to obtain translation response from ' + url)
      })
    },

    translateText: function (textToTranslate, language) {
      let url = 'https://translate.yandex.net/api/v1.5/tr.json/translate?key=' + this.apiKey + '&text=' + textToTranslate + '&lang=' + language
      this.$http.get(url)
      .then(response => {
        this.translatedText = response.body.text[0] // grab first answer
      }, response => {
        console.log('Failed to obtain translation response from ' + url)
      })
    }
  }
}
</script>

<style>
body {
  background-color: #fefefe
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
