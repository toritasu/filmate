<template lang="pug">
  a.fan(
    :href="`user/${fan.id}`"
  )
    img(
      :src="fan.icon"
      :alt="fan.name"
      :class="`-${size}`"
    )
</template>

<script>
export default {
  name: 'FanIcon',
  props: {
    fan: {
      type: Object,
      required: true,
    },
    size: {
      type: String,
      default: 'small',
    },
  },
}
</script>

<style lang="scss" scoped>
$small: 35px;
$medium: 48px;
$large: 124px;
.fan {
  display: inline-block;
  border-radius: 100%;
  img {
    border-radius: 100%;
    object-fit: cover;
  }
  .-small {
    width: $small;
    height: $small;
  }
  .-medium {
    width: $medium;
    height: $medium;
  }
}
</style>
