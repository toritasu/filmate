<template lang="pug">
  li.card(@click="goDetail(video.id, mediaType)")
    div.poster
      picture
        img(:src="getImagePath(video.poster_path, 1)")
    div.content
      div.type
        |{{ getMediaType() }}
      div.title
        |{{ getTitle() }}
      div.release
        |{{ getReleaseYear() }}
      div.genre(v-for="genreId in genreIdsLimited")
        |{{ getGenreName(genreId) }}
      div.fans
        fan-icon(v-for="fan in fansSample" :key="fan.id" :fan="fan")
</template>

<script>
import FanIcon from '@/components/FanIcon'

// TODO: タイトル欄が２行を超えたら文字サイズを小さくするメソッド
// TODO: カテゴリー数は３を超える場合、最後に＋マークをつける
// TODO: 「サイエンスフィクション」をSFにする
export default {
  components: {
    'fan-icon': FanIcon,
  },
  props: {
    video: {
      type: Object,
      default: () => {},
    },
    mediaType: {
      type: String,
      default: 'movie',
    },
    baseUrl: {
      type: String,
      default: '',
    },
    posterSizes: {
      type: Array,
      default: () => [],
    },
    genre: {
      type: Object,
      default: () => ({}),
    },
  },
  // この作品をスキした人（仮のデータ）
  // slotかコンポーネント
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
      fansSample: [],
    }
  },
  computed: {
    genreIdsLimited() {
      return this.video.genre_ids.slice(0, 3)
    },
  },
  created() {
    // console.log(this.video.id + ': ' + this.getTitle())
    // console.log(this.video)

    // ランダムで5人までアイコン表示
    this.getSampleUsers()
  },
  methods: {
    // 詳細ページへ遷移
    goDetail(id, mediaType) {
      console.log('Go To: works/' + id)
      this.$router.push(`/works/${id}?media_type=${mediaType}`)
    },
    // 画像パスを生成
    // poster_sizesはインデックスが上がるほど大きい
    getImagePath(filename, sizeIndex) {
      const width = this.posterSizes[sizeIndex]
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
      } else if (this.video.genre_ids.includes(16)) {
        return 'アニメ'
      } else {
        return 'ドラマ'
      }
    },
    // - サンプル用fans生成
    getSampleUsers() {
      const count = Math.floor(Math.random() * 6)
      const fansTemp = this.fans
      for (let i = 0; i < count; i++) {
        const index = Math.floor(Math.random() * fansTemp.length)
        this.fansSample.push(fansTemp[index])
        fansTemp.splice(index, 1)
      }
    },
  },
}
</script>

<style scoped lang="scss">
* {
  font-family: 'Noto Sans JP', sans-serif;
}
.card {
  width: 100%;
  height: 210px;
  background-color: #ffffff;
  border-radius: 5px;
  display: flex;
  overflow: hidden;
  box-shadow: 0 4px 10px #000;
  &:not(:last-child) {
    margin-bottom: 20px;
  }
  .poster {
    width: 43%;
    height: 100%;
    img,
    source {
      width: 100%;
      height: 100%;
      object-fit: cover;
      border-top-left-radius: 5px;
      border-bottom-left-radius: 5px;
    }
  }
  .content {
    width: 57%;
    margin: 10px;
    overflow: hidden;
    position: relative;
    .type {
      display: inline-block;
      color: #585858;
      font-size: 0.635rem;
      border: 1px solid #6f6f6f;
      border-radius: 3px;
      padding: 4px 10px;
      margin-bottom: 7px;
    }
    .title {
      color: #363636;
      font-size: 1.15rem;
      font-weight: 600;
      margin-bottom: 7px;
    }
    .release {
      color: #585858;
      font-size: 0.75rem;
      font-weight: 600;
      margin-bottom: 14px;
    }
    .genre {
      display: inline-block;
      color: #ffffff;
      font-size: 0.625rem;
      padding: 4px 8px;
      background-color: #ff8933;
      border-radius: 30px;
      margin-right: 4px;
      margin-bottom: 4px;
    }
    .fans {
      position: absolute;
      bottom: 0;
      right: 0;
      a {
        position: absolute;
        bottom: 0;
        right: 0;
      }
      &:first-child {
        z-index: 10;
      }
      @for $cnt from 1 through 5 {
        .fan:nth-child(#{$cnt}).fan:not(:first-child) {
          transform: translateX(($cnt - 1) * -40%);
          z-index: 10 - $cnt;
        }
      }
    }
  }
}
</style>
