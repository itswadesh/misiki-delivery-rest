<template>
  <div>
    <Header />
    <div class="heading">
      <nuxt-link to="/pickup">
        <i class="fa fa-long-arrow-left" />
      </nuxt-link>
      <span v-if="orders[0]">{{ orders[0].vendor.restaurant }}</span> Pickup
      <button @click="getData">
        <i class="fa fa-refresh" />
      </button>
    </div>

    <!-- <div class="flex" v-if="orders.total">
      <h1 style="color:blue">{{ total.total | currency }}</h1>
    </div>-->
    <div class="flex flex-col justify-between smallcard" v-for="f in orders.data" :key="f.id">
      <div class="flex justify-between items-center border-b pt-1">
        <!-- @click="go('/delivery/' + f._id)" -->
        <div>
          <h1 class="text-xl font-black">{{ f._id.address.address }}</h1>
          <a
            class="text-xl bg-blue-500 text-white"
            :href="`tel:+91-${f._id.user.phone}`"
          >Call Customer</a>
        </div>
        <div class="font-black text-green-600 text-3xl">{{ f._id.amount.total | currency }}</div>
      </div>
      <div class="bg-yellow-200 text-black border-b" v-for="(i,ix) in f.items" :key="i.slug">
        {{ix+1}} - {{ i.name }} * {{i.qty}}
        <div v-if="f._id">
          <h2 class="text-xl font-black">{{ i.vendor.restaurant }}</h2>
          <a class="text-xl bg-red-500 text-white" :href="`tel:+91-${i.vendor.phone}`">Call Chef</a>
        </div>
        <div class="flex items-center justify-between">
          <button class="button" @click="save(f._id.id, i.pid, 'out')">Pick</button>
          <button @click="save(f._id.id, i.pid, 'cancelled')">Cancel</button>
        </div>
      </div>
    </div>
    <StickyFooter />
  </div>
</template>
<script>
const Header = () => import('~/components/Header')
const StickyFooter = () => import('~/components/footer/StickyFooter')
import myOrders from '~/gql/order/myOrders.gql'
import updateOrder from '~/gql/order/updateOrder.gql'

// import io from "socket.io-client";
// import { WS_URL } from "~/config";
// let socket = io(WS_URL);
// import { SocketService } from "~/service/socket";
// let ss = new SocketService();
export default {
  data() {
    return {
      orders: [],
      total: {},
      restaurant: null
    }
  },
  created() {
    this.getData()
  },
  components: {
    Header,
    StickyFooter
  },
  methods: {
    cancel(id, status) {
      this.$swal({
        title: 'Are you sure to cancel?',
        type: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, Cancel!'
      }).then(async result => {
        if (result.value) {
          this.save(id, status)
        }
      })
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
    go(url) {
      this.$router.push(url)
    },
    async getData() {
      try {
        this.$store.commit('clearErr')
        this.orders = (
          await this.$apollo.query({
            query: myOrders,
            variables: { id: this.$route.params.id },
            fetchPolicy: 'no-cache'
          })
        ).data.myOrders
        // await ss.syncUpdates("food-order", this.orders);
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
.heading {
  text-align: center;
  color: #fff;
  background: linear-gradient(87deg, #fb6340 0, #da1c5f 100%) !important;
  box-shadow: 0 0.3rem 0.3rem rgba(0, 0, 0, 0.175) !important;
  margin-bottom: 0.5rem;
  padding: 1rem 0;
  font-size: 2rem;
}
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
