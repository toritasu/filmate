<template lang="pug">
  li.review-card
    section.outline
      fan-icon.icon(:fan="fan" size="medium")
      div.user
        span.name
          |{{ fan.name }}さん
        span.status
          |フォロー中
      span.eval
        img(:src="evalIcon")
          
    section.comment(v-if="hasComment")
      h3.heading
        |キャプテンが尊い
      p.body
        |うんたらかんたら
</template>

<script>
export default {
  props: {
    fan: {
      type: Object,
      required: true,
    },
  },
  computed: {
    hasComment() {
      return Math.floor(Math.random() * 2)
    },
    evalIcon() {
      const rand = Math.floor(Math.random() * 2)
      return rand
        ? require('@/assets/images/eval_1.png')
        : require('@/assets/images/eval_2.png')
    },
  },
}
</script>

<style scoped lang="scss">
.review-card {
  width: calc(100% - calc(20px * 2));
  background-color: #ffffff;
  margin-left: auto;
  margin-right: auto;
  &:not(:last-child) {
    margin-bottom: 15px;
  }
  padding: 10px;
  border-radius: 5px;
  .outline {
    display: flex;
    .icon {
      margin-right: 10px;
    }
    .user {
      .name {
        font-size: 0.875rem;
        display: block;
        margin-bottom: 4px;
      }
      .status {
        color: #ffffff;
        font-size: 0.625rem;
        background-color: #4eb4ff;
        padding: 2px 15px;
        border-radius: 0.625rem;
      }
    }
    .eval {
      margin-left: auto;
      img {
        width: 48px;
        height: 48px;
      }
    }
  }
  .comment {
    font-size: 0.875rem;
    padding-top: 15px;
    .heading {
      font-weight: bold;
      margin-bottom: 10px;
    }
  }
}
</style>
