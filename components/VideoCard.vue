<template lang="pug">
  li.card
    div.poster
      picture
        img(:src="getImagePath(video.poster_path || video.profile_path, 1)")
    div.content
      div.type
        |{{ getMediaType() }}
      div.title
        |{{ getTitle() }}
      div.release
        |{{ getReleaseYear() }}
      div.genre(v-for="genreId in video.genre_ids")
        |{{ getGenreName(genreId) }}
</template>

<script>
export default {
  props: {
    video: {
      type: Object,
      default: () => ({})
    },
    mediaType: {
      type: String,
      default: 'movie'
    },
    baseUrl: {
      type: String,
      default: ''
    },
    posterSizes: {
      type: Array,
      default: () => ({})
    },
    genre: {
      type: Object,
      default: () => ({})
    }
  },
  mounted() {
    console.log(this.video.id  + ': '+ this.getTitle())
    console.log(this.video)
  },
  methods: {
    // 画像パスを生成
    // poster_sizesはインデックスが上がるほど大きい
    getImagePath(filename, size_index) {
      const width = this.posterSizes[size_index]
      if(filename) { 
        return this.baseUrl + width + filename
      } else {
        return require('@/assets/images/no-image.png')
      }
    },
    getTitle() {
      if(this.mediaType === 'movie') {
        return this.video.title
      } else {
        return this.video.name
      }
    },
    getReleaseYear() {
      let date = ''
      let append = ''
      if(this.mediaType === 'movie') {
        date = this.video.release_date
        append = '年公開'
      } else {
        date = this.video.first_air_date
        append = '年放映開始'
      }
      return date ? date.substring(0, date.indexOf('-')) + append : ''
    },
    getGenreName(genreId) {
      console.log(genreId)
      let index = 0;
      switch(this.mediaType) {
        case 'movie':
          index = this.genre.movie.findIndex(elm => elm.id === genreId)
          if(index >= 0) { 
            console.log(index)
            return this.genre.movie[index].name
          }
          break
        case 'tv':
          index = this.genre.tv.findIndex((elm) => elm.id === genreId)
          if(index >= 0) { 
            console.log(index)
            return this.genre.tv[index].name
          }
      }
    },
    getMediaType() {
      if(this.mediaType === 'movie') {
        return '映画'
      } else if(this.video.genre_ids.includes(16)) {
        return 'アニメ'
      } else {
        return 'ドラマ'
      }
    }
  }
}
</script>

<style scoped lang="scss">
  * {
    font-family: 'Noto Sans JP', sans-serif;
  }
  .card {
    width: 100%;
    height: 210px;
    background-color: #FFFFFF;
    border-radius: 5px;
    display: flex;
    &:not(:last-child) {
      margin-bottom: 25px;
    }
    .poster {
      width: 43%;
      height: 100%;
      img, source {
        width: 100%;
        height: 100%;
        object-fit: cover;
        border-top-left-radius: 5px;
        border-bottom-left-radius: 5px;
      }
    }
    .content {
      width: 57%;
      padding: 10px;
      .type {
        display: inline-block;
        color: #585858;
        font-size: 0.635rem;
        border: 1px solid #6F6F6F;
        border-radius: 3px;
        padding: 6px 12px;
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
        color: #FFFFFF;
        font-size: 0.625rem;
        padding: 5px 10px;
        background-color: #FF8933;
        border-radius: 30px;
        margin-right: 5px;
        margin-bottom: 5px;
      }
    }
  }
</style>