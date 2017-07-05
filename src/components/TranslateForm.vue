<template>
  <div class="row" id="translateForm">
    <div class="col-md-10 col-md-offset-1">
      <form id="transForm" class="well form-inline" v-on:submit.prevent="transFormSubmit">
        <div class="row">
          <div class="form-group col-md-8">
            <label class="sr-only" for="formInput">Input</label>
            <input type="text" id="textInput" class="form-control" placeholder="Enter text to translate" v-model="textToTranslate">
          </div>
          <div class="form-group col-md-2">
            <select class="form-control" v-model="language">
              <option v-bind:value="key" v-for="(value, key) in languageAll">{{value}}</option>
            </select>
          </div>
          <div class="form-group col-md-2">
            <input type="submit" class="btn btn-primary">
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'translateForm',
  props: ['apiKey'], // api key passed down from parent
  data () {
    return {
      textToTranslate: '',
      language: '',
      languageAll: {}
    }
  },
  created () {
    this.getLangs()
    this.language = 'zh'
  },
  methods: {
    getLangs: function () {
      let url = 'https://translate.yandex.net/api/v1.5/tr.json/getLangs?key=' + this.apiKey + '&ui=en'
      this.$http.get(url)
      .then(response => {
        this.languageAll = response.body.langs
      }, response => {
        console.log('Failed to obtain the list of languages from ' + url)
      })
    },
    transFormSubmit: function () {
      this.$emit('translate', this.textToTranslate, this.language)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#textInput {
  width: 100%;
}
#transForm {
  border-radius: 10px;
  border: 1px #ccc solid;
}
</style>
