<template lang="pug">
  .wrapper
    input(type="text" v-model="searchWords" placeholder="入力してください")
    p
      |{{ searchWords }}
    ul
      video-card(
        v-for = "video in selectedItems"
        :key = "video.id"
        :video = "video"
        :baseUrl = "config.base_url"
        :posterSizes = "config.poster_sizes"
        :genre = "genre"
        )
        br
        
</template>

<script>
import axios from 'axios'
import VideoCard from '@/components/VideoCard'

export default ({
  components: {
    'video-card': VideoCard
  },
  data() {
    return {
      selectedItems: [],
      popularItems: [],
      searchWords: ''
    }
  },
  // asyncDataでapiからデータ取得
  // 基本設定、人気
  async asyncData({ env, error }){
    const baseApi = 'https://api.themoviedb.org/3'
    const lang = 'ja-JP'
    const region = 'JP'
    const page = 1
    try {
      const [configration, popularVideosRes, movieGenre, tvGenre] = await Promise.all([
        axios.get(`${baseApi}/configuration?api_key=${env.apiKey}`),
        axios.get(`${baseApi}/movie/popular?api_key=${env.apiKey}&language=${lang}&page=${page}&region=${region}`),
        axios.get(`${baseApi}/genre/movie/list?api_key=${env.apiKey}&language=${lang}`),
        axios.get(`${baseApi}/genre/tv/list?api_key=${env.apiKey}&language=${lang}`),
      ])

      return {
        config: configration.data.images,
        selectedItems: popularVideosRes.data.results,
        genre: {
          movie: movieGenre.data.genres,
          tv: tvGenre.data.genres
        }
      }
    } catch (e) {
      error(e);
    }
  },
  //-----------------------
  // mounted: 初期設定
  //-----------------------
  mounted() {
    console.log(this.config)
    console.log(this.popularVideosRare)
    console.log(this.genre.movie)
    console.log(this.genre.tv)
    this.popularItems = this.selectedItems
  },
  //-----------------------
  // watch
  //-----------------------
  watch: {
    searchWords: function() {
      if(this.searchWords != '') { 
        this.selectedItems = this.getSearchedVideos()
      } else {
        this.selectedItems = this.popularItems
      }
    }
  },
  //-----------------------
  // methods: 
  //-----------------------
  methods: {
    // マルチサーチ
    async getSearchedVideos() {
      const baseApi = 'https://api.themoviedb.org/3'
      const page = 1
      await axios.get(
        `${baseApi}/search/multi?api_key=${process.env.apiKey}&language=ja&page=${page}&query=${this.searchWords}`
      ).then((res) => {
        console.log('multi-search')
        console.log(res)
        this.selectedItems = res.data.results
      })
    }
  }
})
</script>

<style scoped lang="scss">
  * {
    font-family: 'Noto-sans';
  }
  .wrapper {
    background-color: #131313;
    padding: 0 20px;
  }
</style>