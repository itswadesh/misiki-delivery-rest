<template>
  <div>
    <div class="h-screen">
      <Heading title="Welcome to misiki" />
      {{todaySummary}}=={{todayTotal}}
      <div v-if="orders">
        <div class="heading" v-if="todaysStatus">
          Today's Pickup - {{ todaysStatus.count }}
          <button icon dark @click="getData">
            <i class="fa fa-refresh" />
          </button>
        </div>
        <div class="fx" v-if="todaysStatus">
          <h1 style="color:blue">{{ todaysStatus.total | currency }}</h1>
          <h1 style="color:red">&nbsp;- {{ todaysStatus.total | currency }}</h1>
          <h1
            style="color:green"
          >&nbsp; = {{ (todaysStatus.total - todaysStatus.total) | currency }}</h1>
        </div>
      </div>
    </div>
    <StickyFooter />
    <GeoLocation />
  </div>
</template>

<script>
import GeoLocation from '~/components/GeoLocation.vue'
import Heading from '~/components/Heading.vue'
import StickyFooter from '~/components/footer/StickyFooter'
import pendingOrders from '~/gql/order/pendingOrders.gql'
import myToday from '~/gql/order/myToday.gql'
import todaysSummary from '~/gql/order/todaysSummary.gql'
import todaysStatus from '~/gql/order/delivery.gql'
import updateOrder from '~/gql/order/updateOrder.gql'
export default {
  middleware: ['isAuth'],
  data() {
    return {
      orders: [],
      status: 'Received',
      todayTotal: null,
      todaySummary: null,
      todaysStatus: null
    }
  },
  async mounted() {
    this.getData()
  },
  methods: {
    async getData() {
      try {
        this.$store.commit('clearErr')
        this.todaysStatus = (
          await this.$apollo.query({ query: todaysStatus, variables: {} })
        ).data.todaysStatus
        this.todayTotal = (
          await this.$apollo.query({ query: myToday, variables: {} })
        ).data.myToday
        this.todaySummary = (
          await this.$apollo.query({ query: todaysSummary, variables: {} })
        ).data.todaysSummary
        this.orders = (
          await this.$apollo.query({ query: pendingOrders, variables: {} })
        ).data.pendingOrders
      } finally {
        this.$store.commit('busy', false)
      }
    }
  },
  // async asyncData({ $axios }) {
  //   let orders = [],
  //     status = 'Received',
  //     todayTotal = null,
  //     todaySummary = null
  // try {
  //   // orders = await $axios.$get('api/food-orders/pending')
  //   // todayTotal = await $axios.$get('api/food-orders/my/today')
  //   // todaySummary = await $axios.$get('api/food-orders/todays-summary')
  // } catch (e) {}
  // // return { orders: orders.data, todayTotal, todaySummary }
  // },
  components: {
    GeoLocation,
    Heading,
    StickyFooter
  }
}
</script>
