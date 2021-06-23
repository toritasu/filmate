<template lang="pug">
  a.fan(
    :href="`user/${fan.id}`"
  )
    img(:src="fan.icon" :alt="fan.name")
</template>

<script>
export default {
  name: 'FanIcon',
  props: {
    fan: {
      type: Object,
      required: true,
    },
  },
}
</script>

<style lang="scss" scoped>
.fan {
  display: inline-block;
  border-radius: 100%;
  border: 1px solid #909090;
  img {
    width: 35px;
    height: 35px;
    border-radius: 100%;
    object-fit: cover;
  }
}
</style>
