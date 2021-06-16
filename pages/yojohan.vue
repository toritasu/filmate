<template>
  <div class="wrapper">
    <h1>{{ text }}</h1>
    <ul>
      <li class="box" v-for="item in charas" :key="item.id">
        <div class="flex">
          <img :src="item.image.url" alt="">
          <h2>{{item.title}}</h2>
        </div>
        <p>
          {{item.body}}
        </p>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import axios from 'axios'
import Vue from 'vue'

export default Vue.extend({
  async asyncData() {
    const res = await axios.get(
      'https://tuttofare.microcms.io/api/v1/test',
      {
        headers: { 'X-API-KEY': '97f20344-28db-46b8-9fbf-73b05a56b51e'}
      }
    )
    return { charas: res.data.contents }
  },
  data() {
    return {
      text: '' as string
    }
  },
  created() {
    this.text = '四畳半神話大系'
  }
})
</script>

<style scoped lang="scss">
  * {
    font-family: 'Noto-sans';
  }
  .wrapper {
    padding: 0 20px;
  }
  h1 {
    font-size: 2rem;
    font-weight: 600; 
    text-align: center;
    padding: 20px 0;
  }
  h2 {
    font-size: 1.5rem;
    font-weight: 500;
    line-height: 100px;
    padding: 0 20px;
  }
  .box {
    padding: 20px;
    border-radius: 4px;
  }
  .flex {
    display: flex;
  }
  img {
    width: 100px;
    height: 100px;
    object-fit: cover;
    border-radius: 100%;
    border: 2px solid #9be2ff;
  }
  p {
    margin-top: 20px;
    line-height: 1.5;
  }
  .red {
    color: red;
  }
</style>