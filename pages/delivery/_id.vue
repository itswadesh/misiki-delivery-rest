<template>
  <div>
    <nuxt-link to="/delivery">
      <i class="fa fa-long-arrow-left" />
    </nuxt-link>
    <h3>Customer Details</h3>
    <div v-if="order">
      <div v-if="order.address">Name: {{ order.address.firstName }} {{ order.address.lastName }}</div>
      <div>Phone: {{ order.user.phone }}</div>
      <div v-if="order.address">Address: {{ order.address.address }}</div>

      <h3>Order Details</h3>
      <div>Date: {{ order.createdAt | date }}</div>
      <h3>Item Details</h3>
      <div v-for="i in order.items" :key="i.pid">
        <img v-lazy="i.img" />
        <div>Item: {{ i.name }}</div>
        <div>Delivery Time: {{ i.time }}</div>
        <div>Status: {{ i.status }}</div>
        <div>{{ i.price }}*{{ i.qty }} = {{i.price * i.qty }}</div>
        <h3>Chef Details</h3>
        <div v-if="i.vendor">
          {{ i.vendor.phone }}
          {{ i.vendor.firstName }} {{ i.vendor.lastName }}
          {{ i.vendor.restaurant }}
          {{ i.vendor.address.address }}
        </div>
      </div>

      <div class="flex flex-center">
        <button
          class="button"
          v-if="order.status == 'Order Placed'"
          @click="save(order._id, 'Delivered')"
        >
          <span class="fonttype grn">Delivered</span>
        </button>
        <button
          class="button"
          v-if="order.status !== 'Cancelled'"
          @click="save(order._id, 'Cancelled')"
        >
          <span class="fonttype grn">Cancelled</span>
        </button>
        <button
          v-if="order.status == 'Delivered'"
          class="button"
          @click="save(order._id, 'Order Placed')"
        >
          <span class="fonttype grn">Returned</span>
        </button>
        <button
          v-if="order.status == 'Cancelled'"
          class="button"
          @click="save(order._id, 'Order Placed')"
        >
          <span class="fonttype grn">Put Back</span>
        </button>
        <button
          v-if="order.status == 'Out For Delivery'"
          class="button"
          @click="save(order._id, 'Delivered')"
        >
          <span class="fonttype grn">Delivered</span>
        </button>
        <button
          v-if="order.status == 'Out For Delivery'"
          class="button"
          @click="save(order._id, 'Order Placed')"
        >
          <span class="fonttype grn">Put Back</span>
        </button>
      </div>
    </div>
    <StickyFooter />
  </div>
</template>
<script>
const StickyFooter = () => import('~/components/footer/StickyFooter')
import order from '~/gql/order/order.gql'
import updateOrder from '~/gql/order/updateOrder.gql'

export default {
  middleware: ['isAuth'],
  data() {
    return {
      order: null
    }
  },
  async created() {
    this.getData()
  },
  methods: {
    async getData() {
      try {
        const id = this.$route.params.id
        this.$store.commit('clearErr')
        this.order = (
          await this.$apollo.query({
            query: order,
            variables: { id },
            fetchPolicy: 'no-cache'
          })
        ).data.order
      } catch (e) {
        this.$store.commit('setErr', e)
      } finally {
        this.$store.commit('busy', false)
      }
    },
    async save(id, pid, status) {
      try {
        this.$store.commit('clearErr')
        await this.$apollo.mutate({
          mutation: updateOrder,
          variables: { id, pid, status },
          fetchPolicy: 'no-cache'
        })
        this.getData()
      } catch (e) {
        this.$store.commit('setErr', e, { root: true })
      } finally {
        this.$store.commit('busy', false)
      }
    }
  },
  components: { StickyFooter }
}
</script>
<style scoped>
h3 {
  @apply text-3xl;
}
.button {
  margin-top: 20px;
  width: 100%;
  text-transform: initial;
  color: #fff;
  background: linear-gradient(87deg, #fb6340 0, #da1c5f 100%) !important;
  border-color: #fb6340;
  -webkit-box-shadow: 0 4px 6px rgba(50, 50, 93, 0.11),
    0 1px 3px rgba(0, 0, 0, 0.08);
  box-shadow: 0 4px 6px rgba(50, 50, 93, 0.11), 0 1px 3px rgba(0, 0, 0, 0.08);
  width: 100%;
  display: block;
  font-size: 1.25rem;
  line-height: 1.5;
  border-radius: 0.3rem;
  padding: 7px;
  outline: none;
}
</style>
