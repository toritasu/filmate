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
    section.result#result
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
    section.readmore(v-if="isLoadable")
      button(@click="loadNextPage")
        |次の20件

</template>

<script>
import axios from 'axios'
import VideoCard from '@/components/VideoCard'

// TODO: CompositionAPI & TypeScript 導入
// TODO: ランキング（初回表示）と検索機能のコンポーネントを分ける

export default {
  components: {
    'video-card': VideoCard,
  },
  // asyncDataでapiからデータ取得
  // 基本設定、人気
  async asyncData({ env, error }) {
    const baseApi = 'https://api.themoviedb.org/3'
    const lang = 'ja-JP'
    const region = 'JP'
    const page = 1
    try {
      const [configration, popularVideosRes, movieGenre, tvGenre] =
        await Promise.all([
          axios.get(`${baseApi}/configuration?api_key=${env.apiKey}`),
          axios.get(
            `${baseApi}/movie/popular?api_key=${env.apiKey}&language=${lang}&page=${page}&region=${region}`
          ),
          axios.get(
            `${baseApi}/genre/movie/list?api_key=${env.apiKey}&language=${lang}`
          ),
          axios.get(
            `${baseApi}/genre/tv/list?api_key=${env.apiKey}&language=${lang}`
          ),
        ])

      return {
        config: configration.data.images,
        videos: popularVideosRes.data.results,
        genre: {
          movie: movieGenre.data.genres,
          tv: tvGenre.data.genres,
        },
      }
    } catch (e) {
      error(e)
    }
  },
  data() {
    return {
      popularVideos: [],
      searchedVideos: [],
      searchWords: '',
      resultHeading: '話題の新作',
      totalPages: 0,
      currentPage: 1,
    }
  },
  computed: {
    isLoadable() {
      return this.currentPage < this.totalPages
    },
  },
  // -----------------------
  // watch
  // -----------------------
  watch: {
    searchWords() {
      if (this.searchWords) {
        this.currentPage = 1
        this.searchedVideos = []
        this.getSearchedVideos()
      } else {
        this.videos = this.popularVideos
        this.resultHeading = '話題の新作'
      }
    },
    areaHeight() {
      if (this.areaHeight < window.innerHeight) {
        document.getElementById('result').style.height = window.innerHeight
      }
    },
  },
  // -----------------------
  // mounted: 初期設定
  // -----------------------
  mounted() {
    console.log(this.config)
    console.log(this.genre.movie)
    console.log(this.genre.tv)
    this.popularVideos = this.videos
    this.areaHeight = document.getElementById('result').clientHeight
  },
  // -----------------------
  // methods:
  // -----------------------
  methods: {
    // マルチサーチ
    async getSearchedVideos() {
      const baseApi = 'https://api.themoviedb.org/3'
      await axios
        .get(
          `${baseApi}/search/multi?api_key=${process.env.apiKey}&language=ja&page=${this.currentPage}&query=${this.searchWords}`
        )
        .then((res) => {
          console.log('multi-search')
          console.log(res)
          const data = res.data.results
          for (let i = 0; i < data.length; i++) {
            if (data[i].media_type !== 'person') {
              this.searchedVideos.push(data[i])
            }
          }
          // バグ: personを除外しているせいで、total_pages / total_results の計算が合わない
          // multisearchではなく、movieとtvのsearchを２つ叩く or メニュー分けるのも検討
          this.videos = this.searchedVideos
          this.totalPages = res.data.total_pages
          this.togalResults = res.data.total_results
          this.resultHeading = `${this.togalResults}件見つかりました`
          console.log(this.videos)
        })
    },
    // 次のページ
    loadNextPage() {
      this.currentPage += 1
      this.getSearchedVideos()
    },
  },
}
</script>

<style scoped lang="scss">
$space_m: 20px;

* {
  font-family: 'Noto Sans JP', sans-serif;
}
.wrapper {
  background-color: #3f3f3f;
  height: 100%;
  padding: 0 $space_m $space_m;
}
.heading {
  color: #f2f2f2;
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
    background-color: #ffffff;
    font-size: 1rem;
    padding: 15px;
    text-align: left;
    border-radius: 30px;
  }
}
.result {
  height: 100%;
  margin-bottom: $space_m;
}
.readmore {
  button {
    color: #ffffff;
    background-color: #999999;
    padding: 15px 40px;
    border-radius: 5px;
    display: block;
    margin: 0 auto;
  }
}
</style>
