<template>
  <form v-on:submit="formSubmit">
    <input type="text" v-model="textToTranslate" placeholder="请输入需要翻译的内容"/>
    <input type="submit" value="翻译"/>
  </form>
</template>

<script>
export default {
  name: 'TranslateForm',
  data: function() {
    return {
      textToTranslate: "",
      lang: ""
    }
  },
  methods: {
    formSubmit: function(e) {
      this.$http.get('http://route.showapi.com/32-9?showapi_appid=1385352&showapi_sign=eb5c2e7ae797431db5366bfd821a698c&q=nice&fromLanguage=auto&toLanguage=1')
          .then((response)=> {
            console.log(response.body.showapi_res_body.translation[0])
            // this.$emit("translatedText", response.body.showapi_res_body.basic.explains[0]) //与父组件进行通信
          })
      // this.$emit("translatedText", result)
      e.preventDefault()
    }
  }
}
</script>

<style>

</style>
