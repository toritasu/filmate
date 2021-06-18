<template lang="pug">
  div.wrapper
    section.form
      h2.heading
        |作品をさがす
      input.input(
          type="text"
          v-model="searchWords"
          placeholder="入力してください"
        )
    section.result
      h2.heading()
        |{{ resultHeading }}
      ul
        video-card(
          v-for = "video in videos"
          :key = "video.id"
          :video = "video"
          :baseUrl = "config.base_url"
          :posterSizes = "config.poster_sizes"
          :genre = "genre"
          :mediaType = "video.media_type || 'movie'"
          )
      button.next(v-if="")

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
      popularVideos: [],
      searchedVideos: [],
      searchWords: '',
      resultHeading: '話題の新作'
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
        videos: popularVideosRes.data.results,
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
    console.log(this.selectedItems)
    console.log(this.genre.movie)
    console.log(this.genre.tv)
    this.popularVideos = this.videos
  },
  //-----------------------
  // watch
  //-----------------------
  watch: {
    searchWords: function() {
      if(this.searchWords != '') { 
        this.getSearchedVideos()
      } else {
        this.videos = this.popularVideos
        this.resultHeading = '話題の新作'
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
      this.searchedVideos = []
      await axios.get(
        `${baseApi}/search/multi?api_key=${process.env.apiKey}&language=ja&page=${page}&query=${this.searchWords}`
      ).then((res) => {
        console.log('multi-search')
        console.log(res)
        for(let i = 0; i < res.data.results.length; i++) {
          if(res.data.results[i].media_type != 'person') {
            this.searchedVideos.push(res.data.results[i])
          }
        }
        this.videos = this.searchedVideos
        this.resultHeading = `${res.data.total_results}件見つかりました`
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
    background-color: #212121;
    height: 100%;
    padding: 0 20px;
  }
  .heading {
    color: #F2F2F2;
    font-size: 1rem;
    font-weight: 600;
    margin-bottom: 15px;
    text-align: center;
  }
  .form {
    padding: 30px 0 30px;
    text-align: center;
    .input {
      width: 290px;
      background-color: #FFFFFF;
      font-size: 1rem;
      padding: 15px;
      text-align: left;
      border-radius: 30px;
    }
  }
</style>