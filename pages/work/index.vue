<template lang="pug">
  .wrapper
    ul
      li.box(v-for="video in popularVideosRare" :key="video.id")
        span
          |id:{{ video.id }}
        h3
          |{{video.title}}
        img(:src="getImagePath(video.poster_path, 1)")
        br
        
</template>

<script>
import axios from 'axios'

export default ({
  // asyncDataでapiからデータ取得
  async asyncData({ query, env, error }){
    const baseApi = 'https://api.themoviedb.org/3'
    const words = query.words
    const lang = 'ja-JP'
    const page = 1
    try {
      const configration = await axios.get(
        `${baseApi}/configuration?api_key=${env.apiKey}`
      )
      const searchedVideosRes = await axios.get(
        `${baseApi}/search/multi?api_key=${env.apiKey}&language=${lang}&page=${page}&query=${words}`
      )
      const popularVideosRes = await axios.get(
        `${baseApi}/movie/popular?api_key=${env.apiKey}&language=${lang}&page=${page}`
      )
      console.log(popularVideosRes)
      console.log(configration.data.images)
      return {
        config: configration.data.images,
        searchedVideosRare: searchedVideosRes.data.results,
        popularVideosRare: popularVideosRes.data.results
      }
    } catch (e) {
      error(e);
    }
  },
  mounted() {
    console.log(this.popularVideosRare)
  },
  computed: {
  },
  methods: {
    // 画像パスを生成するメソッド
    // poster_sizesはインデックスが上がるほど大きい
    getImagePath(filename, size) {
      const baseUrl = this.config.base_url
      const width = this.config.poster_sizes[size]
      return baseUrl + width + filename
    }
  }
})
</script>

<style scoped lang="scss">
  * {
    font-family: 'Noto-sans';
  }
  .wrapper {
    padding: 0 20px;
  }
</style>