<template>
  <div>
    <nuxt-link to="/delivery">
      <i class="fa fa-long-arrow-left" />
    </nuxt-link>
    <h3>Order Details</h3>
    <div v-if="order">
      <div class="flex justify-between">
        <div>{{order.payment.method}}</div>
        <div class="text-2xl text-green-500">{{order.payment.amount/100 | currency}}</div>
        <div>{{order.payment.status}}</div>
      </div>
      <div v-if="order.payment.status=='captured'" class="flex justify-between">
        <div>{{order.payment.amount/100 | currency}}</div>
        <!-- <Textbox v-model="cod.amount" label="COD Amount" /> -->
        <div class="flex">
          <Textbox v-model="order.cod_paid" label="COD Paid" />
          <button @click="collectPayment(order.id,order.cod_paid)" class="primary px-4 py-2">Save</button>
        </div>
        <div>Balance: {{order.payment.amount/100 - +order.cod_paid | currency}}</div>
      </div>
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
        <div>{{i.status}}</div>
        <div class="flex flex-center">
          <button
            class="button"
            v-if="i.status == 'Waiting for confirmation'"
            @click="save(order.id, i.pid, 'Delivered')"
          >
            <span class="fonttype grn">Delivered</span>
          </button>
          <button
            class="text-secondary mx-4 my-2"
            v-if="i.status !== 'Cancelled'"
            @click="save(order.id, i.pid, 'Cancelled')"
          >
            <span class>Cancelled</span>
          </button>
          <button
            v-if="i.status == 'Delivered'"
            class="button"
            @click="save(order.id, i.pid, 'Waiting for confirmation')"
          >
            <span class="fonttype grn">Returned</span>
          </button>
          <button
            v-if="i.status == 'Cancelled'"
            class="button"
            @click="save(order.id, i.pid, 'Waiting for confirmation')"
          >
            <span class="fonttype grn">Put Back</span>
          </button>
          <button
            v-if="i.status == 'Out For Delivery'"
            class="button"
            @click="save(order.id, i.pid, 'Delivered')"
          >
            <span class="fonttype grn">Delivered</span>
          </button>
          <button
            v-if="i.status == 'Out For Delivery'"
            class="button"
            @click="save(order.id, i.pid, 'Waiting for confirmation')"
          >
            <span class="fonttype grn">Put Back</span>
          </button>
        </div>
      </div>
    </div>
    <StickyFooter />
  </div>
</template>
<script>
const StickyFooter = () => import('~/components/footer/StickyFooter')
const Textbox = () => import('~/components/ui/Textbox')
const Heading = () => import('~/components/Heading')
import order from '~/gql/order/order.gql'
import updateOrder from '~/gql/order/updateOrder.gql'
import collectPayment from '~/gql/order/collectPayment.gql'

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
    },
    async collectPayment(id, cod_paid) {
      try {
        this.$store.commit('clearErr')
        await this.$apollo.mutate({
          mutation: collectPayment,
          variables: { id, cod_paid: +cod_paid },
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
  components: { StickyFooter, Textbox, Heading }
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
