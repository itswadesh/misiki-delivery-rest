<template>
  <div>
    <Heading title="Today's Delivery" />
    <div class="m-2 flex justify-between">
      <a
        class="p-2 bg-gray-200 rounded shadow"
        href="/api/food-orders/export/500"
        >Export Today's</a
      >
      <a
        class="p-2 bg-gray-200 rounded shadow"
        href="/api/food-orders/dailyTotalByChef"
        >Total by date</a
      >
      <a
        class="p-2 bg-gray-200 rounded shadow"
        href="/api/food-orders/dailyTotalByItem"
        >Total by item</a
      >
    </div>
    <div>
      <div class="flex justify-center" v-if="todayTotal">
        <h2>{{ todayTotal.count }}</h2>
        <h1>{{ todayTotal.total | currency }}</h1>
      </div>
      <ul class="p-left">
        <li class="card" v-for="(o, ix) in orders" :key="ix">
          <div class="font">
            <h1 class="seller">
              {{ o._id.restaurant }} - {{ o._id.phone }}
              <span style="color:green">{{ o._id.qrno }}</span>
            </h1>
          </div>
          <div v-for="(i, ixx) in o.data" :key="'i-' + ixx" class="customer">
            <div class="pb-1">{{ i.name }} ({{ i.phone }})</div>
            <div class="jc-sb">
              <div class="pb-1" style="color:#333">{{ i.item }}</div>
              <div class="pb-1">
                {{ i.qty }} * {{ i.rate | currency }} =
                {{ i.amount | currency }}
              </div>
              <div style="color:red">{{ i.qrno }}</div>
            </div>
            <div class="center">
              <v-btn-toggle v-model="i.status" @change="save(i)">
                <v-btn text value="Cancelled" class="btn4 font">
                  Cancelled
                </v-btn>
                <v-btn text value="Prepared" class="btn1 font">
                  Prepared
                </v-btn>
                <v-btn text value="Out For Delivery" class="btn2 font">
                  Out For Delivery
                </v-btn>
                <v-btn text value="Delivered" class="btn3 font">
                  Delivered
                </v-btn>
              </v-btn-toggle>
            </div>
            <div style="color:violet;font-size:0.7rem;text-align:center">
              {{ i.createdAt | date }}
            </div>
          </div>
        </li>
      </ul>
    </div>
    <nuxt-link to="/my/food/delivery/old/" class="history-button"
      >Old Delivery</nuxt-link
    >
  </div>
</template>
<script>
const Heading = () => import("~/components/Heading");
// import io from "socket.io-client";
// import { WS_URL } from "~/config";
// let socket = io(WS_URL);
export default {
  data() {
    return {
      orders: [],
      status: "Received",
      todayTotal: null
    };
  },
  async created() {
    try {
      this.$store.commit("busy", true);
      this.orders = await this.$axios.$get("api/food-orders/group");
      this.todayTotal = await this.$axios.$get("api/food-orders/summary/today");
    } catch (e) {
    } finally {
      this.$store.commit("busy", false);
    }
    let axios = this.$axios;
    let vm = this;
    // socket.on("food-order" + ":save", async function(item) {
    //   vm.orders = await axios.$get("food-orders/group");
    //   vm.todayTotal = await axios.$get("food-orders/summary/today");
    // });
  },
  components: { Heading },
  methods: {
    async save(o) {
      try {
        await this.$axios.$put("api/food-orders/" + o._id, {
          status: o.status
        });
      } catch (e) {}
    },
    go(url) {
      this.$router.push(url);
    }
  }
};
</script>

<style scoped>
h1 {
  margin: 0px 0px 10px 0px;
}
li .customer {
  border-bottom: 1px solid #ccc;
}
ul > li {
  list-style: none;
  margin: 10px;
  padding: 10px;
}
.card {
  padding: 1rem;
  margin: 1rem;
  -webkit-box-shadow: 0 -0.1rem 1.1rem rgba(0, 0, 0, 0.175) !important;
  box-shadow: 0 -0.1rem 1.1rem rgba(0, 0, 0, 0.175) !important;
  border-radius: 0.5rem;
}
.p-left {
  padding-left: 0px;
}
.seller {
  font-size: 1.4rem;
}
.customer {
  padding: 1rem;
  font-size: 1.2rem;
  color: blue;
  font-weight: 500;
}

.font {
  padding-left: 1rem;
  font-size: 0.7rem;
  padding-left: 0.5rem !important;
  font-weight: 900;
}
</style>
