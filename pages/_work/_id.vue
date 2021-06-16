<template lang="pug">
  p
    |Hello world!
</template>

<script lang="ts">
import axios from 'axios'
import Vue from 'vue'

export default Vue.extend({
  data() {
    return {
      lang: 'ja-JP' as string,
      page: 1,
      query: '',
      base_url: 'http://image.tmdb.org/t/p/',
      posterWidth: 'w342',
    }
  },
  // asyncDataはコンポーネントインスタンス(this)にアクセスすることはできません。
  // その代わりに、contextを引数として受け取ります
  async asyncData({ params, query, app, env, error }) {
    const id = params.id
    const media = query.media_type
    const lang = app.context.lang
    try {
      const res = await axios.get(`https://api.themoviedb.org/3/${media}/${id}?language=${lang}&?api_key=${env.apiKey}`);
      return { res };
    } catch (e) {
      error(e);
    }
  },
  mounted() {
    console.log(process.env.API_KEY)
  },
  computed: {
    currentLang() {
      if(this.lang === 'en' || this.lang === undefined) {
        return 'English';
      } else {
        return '日本語';
      }
    }
  }
})
</script>

<style scoped lang="scss">
  * {
    font-family: 'Noto-sans';
  }
</style>