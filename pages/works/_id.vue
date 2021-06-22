<template lang="pug">
  div.video
    section.key-visual
      img.backdrop(:src="getImagePath(video.backdrop_path, 1)", alt="alt")
      div.top-info
        div.type
          |{{ getMediaType() }}
        div.title
          |{{ getTitle() }}
        div.tagline
          |{{ video.tagline }}
        div.release
          |{{ getReleaseYear() }}
        div.genre(v-for="genreId in video.genres")
          |{{ getGenreName(genreId) }}
    section.fans
      |ユイさん他3名オススメ!!
    section.story
      h2
        |STORY
      p
        |{{video.overview}}

</template>

<script lang="ts">
import axios from 'axios'
import Vue from 'vue'

export default Vue.extend({
  async asyncData({ env, error, query, params }) {
    const baseApi = 'https://api.themoviedb.org/3'
    const lang = 'ja-JP'
    try {
      const [configration, movieGenre, tvGenre, details, casts] =
        await Promise.all([
          axios.get(`${baseApi}/configuration?api_key=${env.apiKey}`),
          axios.get(
            `${baseApi}/genre/movie/list?api_key=${env.apiKey}&language=${lang}`
          ),
          axios.get(
            `${baseApi}/genre/tv/list?api_key=${env.apiKey}&language=${lang}`
          ),
          axios.get(
            `${baseApi}/${query.media_type}/${params.id}?api_key=${env.apiKey}&language=${lang}`
          ),
          // - キャストは別API / しかも映画とTV別
          axios.get(
            `${baseApi}/${query.media_type}/${params.id}/credits?api_key=${env.apiKey}&language=${lang}`
          ),
        ])

      console.log(configration.data.images)
      console.log(details.data)
      return {
        baseUrl: configration.data.images.base_url,
        posterSizes: configration.data.images.poster_sizes,
        backdropSizes: configration.data.images.backdrop_sizes,
        video: details.data,
        genre: {
          movie: movieGenre.data.genres,
          tv: tvGenre.data.genres,
        },
        casts: casts.data,
      }
    } catch (e) {
      error(e)
    }
  },
  data() {
    return {
      funs: [
        {
          id: 1,
          name: 'ユイ',
          icon: require('@/assets/images/sample_users/yui.jpeg'),
        },
        {
          id: 2,
          name: 'にこむ',
          icon: require('@/assets/images/sample_users/niko.jpeg'),
        },
        {
          id: 3,
          name: '松田信介',
          icon: require('@/assets/images/sample_users/matsuda.jpeg'),
        },
        {
          id: 4,
          name: 'クロスケ',
          icon: require('@/assets/images/sample_users/kurosuke.jpeg'),
        },
        {
          id: 5,
          name: 'とよもも',
          icon: require('@/assets/images/sample_users/toyomomo.jpeg'),
        },
        {
          id: 6,
          name: 'とりたす',
          icon: require('@/assets/images/sample_users/toritas.jpeg'),
        },
        {
          id: 7,
          name: 'キャバ嬢',
          icon: require('@/assets/images/sample_users/rie.png'),
        },
      ],
      fansSample: [],
      mediaType: this.$route.query.media_type,
    }
  },
  methods: {
    // 画像パスを生成
    // poster_sizesはインデックスが上がるほど大きい
    getImagePath(filename, sizeIndex) {
      console.log(this.backdropSizes)
      const width = this.backdropSizes[sizeIndex]
      if (filename) {
        return this.baseUrl + width + filename
      } else {
        return require('@/assets/images/no-image.png')
      }
    },
    getTitle() {
      if (this.mediaType === 'movie') {
        return this.video.title
      } else {
        return this.video.name
      }
    },
    getReleaseYear() {
      let date = ''
      let append = ''
      if (this.mediaType === 'movie') {
        date = this.video.release_date
        append = '年公開'
      } else {
        date = this.video.first_air_date
        append = '年放送開始'
      }
      return date ? date.substring(0, date.indexOf('-')) + append : ''
    },
    getGenreName(genreId) {
      let index = 0
      switch (this.mediaType) {
        case 'movie':
          index = this.genre.movie.findIndex((elm) => elm.id === genreId)
          if (index >= 0) {
            return this.genre.movie[index].name
          }
          break
        case 'tv':
          index = this.genre.tv.findIndex((elm) => elm.id === genreId)
          if (index >= 0) {
            return this.genre.tv[index].name
          }
      }
    },
    getMediaType() {
      if (this.mediaType === 'movie') {
        return '映画'
      } else if (this.video.genres.includes(16)) {
        return 'アニメ'
      } else {
        return 'ドラマ'
      }
    },
  },
})
</script>

<style scoped lang="scss">
* {
  font-family: 'Noto Sans JP', sans-serif;
}
.video {
  background-color: #3f3f3f;
  .key-visual {
    position: relative;
    height: 210px;
    .backdrop {
      width: 100%;
      height: 210px;
      object-fit: cover;
      position: absolute;
      z-index: 1;
    }
    .top-info {
      width: 100%;
      height: 210px;
      position: absolute;
      background-color: rgba(0, 0, 0, 0.3);
      z-index: 2;
      padding: 10px 20px;
      color: #ffffff;
    }
  }
  .fans {
    color: #ffffff;
    font-size: 0.75rem;
    text-align: center;
    padding: 20px;
  }
  .story {
    color: #ffffff;
    text-align: center;
    padding: 0 20px;
    margin-bottom: 30px;
    h2 {
      font-size: 1.25rem;
      margin-bottom: 15px;
    }
    p {
      font-size: 0.875rem;
      line-height: 1.7;
    }
  }
}
</style>
