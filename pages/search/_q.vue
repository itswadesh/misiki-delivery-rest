<template>
  <div>
    <!-- <Banner /> -->
    <div class="container relative">
      <!-- <Categories /> -->
      <nuxt-link class="absolute top-0 right-0 fab z-10" to="/foods/new">
        <i class="fa fa-plus" />
      </nuxt-link>
      <div
        class="flex flex-wrap"
        v-infinite-scroll="loadMore"
        :infinite-scroll-distance="3"
        :infinite-scroll-immediate-check="true"
      >
        <div class="w-full" v-for="p in data" :key="p._id">
          <ListCard :p="p" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Heading from '~/components/Heading'
import Banner from '~/components/Banner'
import Product from '~/components/Product'
import ListCard from '~/components/ListCard'
import Categories from '~/components/Categories'
import Loading from '~/components/ui/Loading'
import { query, infiniteScroll } from '~/mixins'
import { TITLE, DESCRIPTION, KEYWORDS, sharingLogo } from '~/config'
import { constructURL } from '~/lib/'
import myProducts from '~/gql/product/myProducts.gql'

export default {
  middleware: 'isAuth',
  layout: 'search',
  mixins: [infiniteScroll],
  data() {
    return {
      model: myProducts,
      attr: 'myProducts'
    }
  },
  components: {
    Heading,
    Banner,
    Product,
    Categories,
    ListCard,
    Loading
  }
}
</script>
