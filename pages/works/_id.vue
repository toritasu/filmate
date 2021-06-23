<template lang="pug">
  div.video
    section.key-visual
      img.backdrop(:src="getImagePath(video.backdrop_path, 1)", alt="alt")
      div.top-info
        //- TODO:コンポーネント化
        div.type
          |{{ getMediaType() }}
        div.title
          |{{ getTitle() }}
        div.tagline
          |{{ video.tagline }}
        div.genre(v-for="genre in video.genres")
          |{{ genre.name }}
    section.fans(v-if="fansSample")
      h2(v-if="fansSample[0].name")
        |{{ fansHeading }}
      p
        fan-icon(
          v-for="fan in fansSample"
          :key="fan.id"
          :fan="fan"
        )
    section.story(v-if="video.overview")
      h2.heading
        |STORY
      p
        |{{video.overview}}
    section.casts(v-if="casts")
      h2.heading
        |CAST
      ul
        cast-card(
          v-for="cast in casts"
          :key="cast.id"
          :cast="cast"
          :baseUrl="baseUrl"
          :profileSizes="profileSizes"
        )
    section.reviews
      h2.heading
        |REVIEWS
      ul
        review-card(
          v-for="fan in fans"
          :key="fan.key"
          :fan="fan"
        )
</template>

<script>
import axios from 'axios'
import Vue from 'vue'
import CastCard from '@/components/CastCard'
import FanIcon from '@/components/FanIcon'
import ReviewCard from '@/components/ReviewCard'

// TODO:小さなパーツのコンポーネント化
// コンポーネント化できないものは、CompositionAPI導入後に関数の切り分け

export default Vue.extend({
  component: {
    'cast-card': CastCard,
    'fan-icon': FanIcon,
    'review-card': ReviewCard,
  },
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
      console.log(casts.data)
      return {
        baseUrl: configration.data.images.base_url,
        posterSizes: configration.data.images.poster_sizes,
        profileSizes: configration.data.images.profile_sizes,
        backdropSizes: configration.data.images.backdrop_sizes,
        video: details.data,
        genre: {
          movie: movieGenre.data.genres,
          tv: tvGenre.data.genres,
        },
        casts: casts.data.cast,
      }
    } catch (e) {
      error(e)
    }
  },
  data() {
    return {
      fans: [
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
      mediaType: this.$route.query.media_type,
    }
  },
  computed: {
    // デバッグ用：ファンを5名までランダム抽出
    fansSample() {
      const count = Math.floor(Math.random() * 6)
      const fansSample = []
      const fansTemp = this.fans
      for (let i = 0; i < count; i++) {
        const index = Math.floor(Math.random() * fansTemp.length)
        fansSample.push(fansTemp[index])
        fansTemp.splice(index, 1)
      }
      return fansSample
    },
    // デバッグ用：ファン見出し生成
    fansHeading() {
      const firstFan = this.fansSample[0].name
      const count = this.fansSample.length - 1
      const rest = count ? `他${count}名` : ''
      return `${firstFan}さん${rest}のオススメ!!`
    },
  },
  created() {
    // デバッグ用
    console.log(this.genre)
    for (let i = 0; i < this.genre.movie.length; i++) {
      console.log(this.genre.movie[i].id + ': ' + this.genre.movie[i].name)
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
    // TODO: モジュール化
    getTitle() {
      if (this.mediaType === 'movie') {
        return this.video.title
      } else {
        return this.video.name
      }
    },
    // TODO: モジュール化
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
  background-color: #2f2f2f;
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
      .type {
        display: inline-block;
        color: #ffffff;
        font-size: 0.635rem;
        border: 1px solid #ffffff;
        border-radius: 3px;
        padding: 4px 10px;
        margin-bottom: 7px;
      }
      .title {
        font-size: 1.5rem;
        font-weight: bold;
        line-height: 1;
        margin-bottom: 5px;
      }
      .tagline {
        font-size: 0.75rem;
        margin-bottom: 10px;
      }
      .genre {
        display: inline-block;
        color: #ffffff;
        font-size: 0.625rem;
        padding: 4px 8px 5px;
        background-color: #ff8933;
        border-radius: 30px;
        margin-right: 4px;
        margin-bottom: 4px;
      }
    }
  }
  .fans {
    color: #ffffff;
    font-size: 0.75rem;
    text-align: center;
    padding: 20px;
    margin-bottom: 20px;
    position: relative;
    text-align: center;
    h2 {
      margin-bottom: 10px;
    }
    .fan:not(:last-child) {
      margin-right: 5px;
    }
  }
  .heading {
    color: #ffffff;
    margin-bottom: 30px;
    font-size: 1.25rem;
    margin-bottom: 15px;
    text-align: center;
  }
  .story {
    padding: 0 20px;
    padding-bottom: 50px;
    p {
      color: #ffffff;
      font-size: 0.875rem;
      line-height: 1.5;
    }
  }
  .casts {
    padding-bottom: 50px;
    ul {
      display: flex;
      flex-wrap: nowrap;
      overflow-x: scroll;
      overflow-y: hidden;
    }
  }
  .reviews {
    padding-bottom: 50px;
  }
}
</style>
