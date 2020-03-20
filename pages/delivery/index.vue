<template>
  <div>
    <Header />
    <div class="heading">
      Today's Delivery
      <button icon dark @click="getData">
        <i class="fa fa-refresh" />
      </button>
    </div>
    <div v-if="orders">
      <div class="flex justify-center text-2xl">
        <h1 class="text-blue-500">{{ orders.delivery.all.total | currency }}</h1>
        <h1 class="text-red-500">&nbsp;- {{ orders.delivery.cancelled.total | currency }}</h1>
        <h1
          class="text-green-500"
        >&nbsp; = {{ (orders.delivery.all.total - orders.delivery.cancelled.total) | currency }}</h1>
      </div>
      <div class="flex justify-center" v-if="orders">
        <Radio
          v-model="status"
          value="Prepared"
          class="text-xs mr-2"
          v-if="orders.delivery.pending"
          @change="getData"
        >Pending ({{ orders.delivery.pending.count }})</Radio>
        <Radio
          v-model="status"
          value="out"
          class="text-xs mr-2"
          v-if="orders.delivery.out"
          @change="getData"
        >Out ({{ orders.delivery.out.count }})</Radio>
        <Radio
          v-model="status"
          value="delivered"
          class="text-xs mr-2"
          v-if="orders.delivery.delivered"
          @change="getData"
        >Delivered ({{ orders.delivery.delivered.count }})</Radio>
        <Radio
          v-model="status"
          value="cancelled"
          class="text-xs mr-2"
          v-if="orders.delivery.cancelled"
          @change="getData"
        >Cancelled ({{ orders.delivery.cancelled.count }})</Radio>
        <Radio
          v-model="status"
          value
          class="text-xs mr-2"
          v-if="orders.delivery.all"
          @change="getData"
        >All ({{ orders.delivery.all.count }})</Radio>
      </div>
      <div v-if="deliveries">
        <div
          class="flex flex-col justify-between smallcard"
          v-for="f in deliveries.data"
          :key="f._id"
        >
          <div class="flex justify-between border-b pt-1" @click="go('/delivery/' + f._id.id)">
            <div v-if="f._id.vendor">
              <h2 class="text-xl">{{ f._id.vendor.address }}</h2>
              <div class="font-bold">{{ f._id.vendor.phone }}</div>
              <div class="text-gray-800">{{ f._id.vendor.restaurant }}</div>
            </div>
            <div v-if="f._id.address">
              <h1 class="text-xl">{{ f._id.address.address }}</h1>
              <div class="font-bold">{{ f._id.address.phone }}</div>
              <div class="text-gray-800">{{ f._id.address.firstName }}</div>
            </div>
            <div
              class="font-bold text-green-500 text-3xl"
              v-if="f._id.amount"
            >{{ f._id.amount.total | currency }}</div>
          </div>
          <div
            class="bg-yellow-100"
            v-for="i in f.items"
            :key="i.pid"
          >{{i.name}} {{i.price | currency}}* {{i.qty}} = {{i.price * i.qty | currency}}</div>
        </div>
      </div>
    </div>
    <StickyFooter />
  </div>
</template>
<script>
const Header = () => import('~/components/Header')
const Radio = () => import('~/components/ui/Radio')
const StickyFooter = () => import('~/components/footer/StickyFooter')
import delivery from '~/gql/order/delivery.gql'
import deliveryOrders from '~/gql/order/deliveryOrders.gql'

// import io from "socket.io-client";
// import { WS_URL } from "~/config";
// let socket = io(WS_URL);
// import { SocketService } from "~/service/socket";
// let ss = new SocketService();
export default {
  data() {
    return {
      status: 'Prepared',
      orders: null,
      deliveries: null
    }
  },
  created() {
    this.getData()
  },
  components: { Header, Radio, StickyFooter },
  methods: {
    go(url) {
      this.$router.push(url)
    },
    async getData() {
      try {
        this.$store.commit('clearErr')
        this.orders = (
          await this.$apollo.query({
            query: delivery,
            fetchPolicy: 'no-cache'
          })
        ).data
        this.deliveries = (
          await this.$apollo.query({
            query: deliveryOrders,
            variables: { status: this.status },
            fetchPolicy: 'no-cache'
          })
        ).data.deliveryOrders
      } catch (e) {
        this.$store.commit('setErr', e)
      } finally {
        this.$store.commit('busy', false)
      }
    }
  }
}
</script>

<style scoped>
h1 {
  color: red;
}
h2 {
  color: grey;
}
.fx {
  display: flex;
  justify-content: center;
}
.js-bt {
  justify-content: space-between;
}
.smallcard {
  padding: 1rem;
  margin-left: 1rem;
  margin-right: 1rem;
  border-radius: 5px;
  margin-top: 1rem;
  box-shadow: 0 15px 35px rgba(50, 50, 93, 0.1), 0 5px 15px rgba(0, 0, 0, 0.07) !important;
}
.smallFont {
  padding-left: 1rem;
  font-size: 0.7rem;
  padding-left: 0.5rem !important;
  font-weight: 900;
}
.block {
  display: none;
}
.fonttype {
  font-size: 20px;
  font-weight: 500;
}
.pb-1rm {
  padding-bottom: 1rem;
}
.pt-1rem {
  padding-top: 1rem;
}
.grn {
  color: green;
}
.redclr {
  color: red;
}
.blueclr {
  color: royalblue;
}
.border-b {
  border-bottom: 1px solid #c5bfbf;
}
</style>
