<template lang="pug">
  li.cast-card
    img.profile(:src="getImagePath(cast.profile_path, 1)" :alt="cast.name")
    div.text
      span.character
        |{{ cast.character }}
      span.actor
        |{{ cast.name }}

</template>

<script>
export default {
  name: 'CastCard',
  props: {
    cast: {
      type: Object,
      required: true,
    },
    baseUrl: {
      type: String,
      default: '',
    },
    profileSizes: {
      type: Array,
      default: () => [],
    },
  },
  created() {},
  methods: {
    getImagePath(filename, sizeIndex) {
      const width = this.profileSizes[sizeIndex]
      if (filename) {
        return this.baseUrl + width + filename
      } else {
        return require('@/assets/images/no-image.png')
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.cast-card {
  display: inline-block;
  width: 100px;
  height: 200px;
  background-color: #ffffff;
  border-left: 1px solid #777777;
  border-bottom: 1px solid #777777;
  box-sizing: border-box;
  .profile {
    width: 100px;
    height: 140px;
  }
  .text {
    height: 60px;
    padding: 5px 8px;
    color: #333333;
    text-align: left;
    overflow-y: scroll;
    span {
      display: block;
      font-size: 0.625rem;
      line-height: 1.2;
      word-break: break-all;
    }
    .character {
      font-weight: bold;
      margin-bottom: 4px;
    }
  }
}
</style>
