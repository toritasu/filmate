<template>
  <div>
    {{ text }}
  </div>
</template>

<script lang="ts">
import Logo from '~/components/Logo.vue'
import Vue from 'vue';

export default Vue.extend({
  data() {
    return {
      text: '' as string
    }
  },
  components: {
    Logo
  },
  created() {
    this.text = 'Hello World!'
  }
})
</script>

<style scoped style="scss">

</style>