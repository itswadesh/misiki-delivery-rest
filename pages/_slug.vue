<template>
  <div class="bg-white">
    <h1 v-if="!product">Not found</h1>
    <div v-else class="relative">
      <div class="z-10 flex absolute w-full items-center justify-between px-4 py-3 sm:p-0">
        <button
          @click="$router.go(-1)"
          class="mt-1 px-auto py-auto text-center absolute rounded-full left-0 w-10 h-10"
        >
          <i class="fa fa-long-arrow-left text-white" />
        </button>
        <nuxt-link
          to="/search"
          class="mt-3 px-auto py-auto text-center absolute rounded-full right-0 w-10 h-10"
        >
          <i class="fa fa-search text-white" />
        </nuxt-link>
      </div>
      <div
        v-lazy:background-image="`${product.img}`"
        class="bg-contain h-48 relative flex justify-center"
      >
        <div
          class="w-3/4 text-xl bg-black text-center py-2 absolute bottom-0 text-white font-bold"
          style="background-color: rgba(0,0,0,.5);"
        >
          <!-- <div class="opacity-100 text-white"></div> -->
          {{ product.restaurant }}
        </div>
      </div>

      <!-- <img v-lazy="`${product.img}`" alt class="w-full object-cover h-48 mb-2" /> -->
      <div class="rounded-t-lg z-10 px-4">
        <div class="py-5">
          <div class="flex justify-between items-center text-gray-600 text-sm">
            <img :src="product.type === 'V' ? 'veg.png' : 'non-veg.png'" class="w-5" />
            <div>
              <i class="fa fa-history" /> 33min
            </div>
            <div>
              <i class="fa fa-map-marker" /> 27kms
            </div>
            By {{ product.restaurant }}
            <div v-if="product.stock < 5">Only {{ product.stock }} left</div>
          </div>
          <h1 class="font-bold text-xl">{{ product.name }}</h1>
          <!-- <div class="flex justify-between items-center text-sm mt-4">
            <div class="bg-orange-500 rounded-full px-3 text-white">free delivery</div>
            <div class="text-gray-600">
              <i class="fa fa-history" /> 33min
            </div>
            <div class="text-gray-600">
              <i class="fa fa-map-marker" /> 27kms
            </div>
          </div>-->
        </div>
        <div class="flex justify-between">
          <!-- <div class="flex">
            <i class="fa fa-star h-10 text-white bg-yellow-500 rounded-full px-2 py-3"></i>
            <div class="mx-3 leading-tight">
              <div class="font-bold">{{ product.rating }}</div>
              <div class="text-gray-500 font-semibold">Ratings</div>
            </div>
          </div>-->
          <h2 class="text-2xl font-bold">{{ product.rate | currency }}</h2>
        </div>
        <div class="font-semibold pb-3 text-xs px-5">{{ product.description }}</div>
      </div>
      <hr class="mb-4" />
      <div v-if="product.vendor">
        <h3 class="font-bold px-3 text-2xl">About Mom</h3>
        <div class="flex bg-gray-100 rounded-lg p-6 mb-6">
          <img
            class="h-24 w-24 rounded-full mx-0 mr-6"
            src="https://randomuser.me/api/portraits/women/17.jpg"
          />
          <div class="text-left">
            <h2 class="text-lg">{{ product.vendor.firstName }} {{ product.vendor.lastName }}</h2>
            <div class="text-gray-600 text-xs tracking-wide">
              <i class="fa fa-map-marker" />
              {{ product.vendor.city }}
            </div>
            <div class="text-green-500 text-sm">
              <i class="fa fa-star" />
              {{ product.vendor.rating }} 120 reviews
            </div>
            <div
              class="text-gray-600"
              v-if="product.vendor.info"
            >{{ product.vendor.info.speciality }}</div>
          </div>
        </div>
        <h3 class="font-bold px-3 text-2xl">Mom's Today's Menu</h3>
        <div class="flex flex-wrap px-2 py-3 mb-4">
          <div class="px-2 w-1/3">
            <img src="/seattle.jpg " class="object-contain rounded-lg shadow" />
          </div>
          <div class="px-2 w-1/3">
            <img src="/seattle.jpg " class="object-contain rounded-lg shadow" />
          </div>
          <div class="px-2 w-1/3">
            <img src="/seattle.jpg " class="object-contain rounded-lg shadow" />
          </div>
        </div>
        <h3 class="font-bold px-3 text-2xl">Kitchen Photos</h3>
        <div class="flex px-2 py-3 mb-4">
          <div class="px-2 w-1/3">
            <img src="/seattle.jpg " class="object-contain rounded-lg shadow" />
          </div>
          <div class="px-2 w-1/3">
            <img src="/seattle.jpg " class="object-contain rounded-lg shadow" />
          </div>
          <div class="px-2 w-1/3">
            <img src="/seattle.jpg " class="object-contain rounded-lg shadow" />
          </div>
        </div>
      </div>
    </div>
    <!-- <StickyFooter class /> -->
  </div>
</template>

<script>
import StickyFooter from '~/components/footer/StickyFooter'
import { mapGetters } from 'vuex'
export default {
  components: { StickyFooter },
  data() {
    return {
      shake: false,
      product: null,
      userSelectedVariant: null
    }
  },
  async created() {
    try {
      this.product = await this.$axios.$get(
        `/api/foods/slug/${this.$route.params.slug}`
      )
      this.userSelectedVariant = this.product.variants[0]
    } catch (e) {}
  },
  methods: {
    selectVariant(s) {
      this.userSelectedVariant = s
      this.$emit('variantChanged', s)
      this.selectedImgIndex = 0
    }
  },
  computed: {
    // ...mapGetters({
    //   checkCart: "cart/checkCart"
    // }),
    user() {
      return (this.$store.state.auth || {}).user || null
    },
    calculateOffPercent() {
      if (!this.product || !this.product.variants[0]) return 0
      let percent =
        ((this.product.variants[0].mrp - this.product.variants[0].price) *
          100) /
        this.product.variants[0].mrp
      return Math.round(percent)
    },
    calculatePrice() {
      let price = 0
      if (this.product.variants[0].price < this.product.variants[0].mrp) {
        price = this.product.variants[0].price
      } else {
        price = this.product.variants[0].mrp
      }
      return price
    }
  }
}
</script>

<style></style>
