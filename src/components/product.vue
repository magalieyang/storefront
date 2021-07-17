<template>
  <a class="products" href="#" v-if="product.article.length > 0">
    <picture>
      <img :src="require('../assets/img/' + product.photo )" alt="" width="300" height="300">
    </picture>
      <div class="description">
      <p >{{ product.article }}</p>
      <p class="h-color-grey">Chaussure <span v-if="product.sexe.length > 0">pour {{ product.sexe }}</span></p>
      <p class="h-color-grey" v-if=" product.couleurNbr > 0">{{  product.couleurNbr }} couleur<span v-if=" product.couleurNbr > 1">s</span>  </p>

      <p>{{ product.prix }}</p>
    </div>

  </a>
</template>

<script>

export default {
  name: 'Product',
  props: ['product'],
  data() {
    return {
    }
  },

}
</script>