<template>
  <v-layout>
    <v-flex text-xs-center>
      <h1>{{ productID }}</h1>
      <h1>{{ name }}</h1>
      <h1>{{ pictureURL }}</h1>
    </v-flex>
  </v-layout>
</template>
<!--
<script>
import axios from 'axios'

export default {
  async asyncData () {
    let { data } = await axios.get(`https://handpicked-refreshments.herokuapp.com/api/product/1`)
    return { productID: data.productID, name: data.name, pictureURL: data.pictureURL }
  }
}
</script>