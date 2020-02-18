<template>
  <div>
    <Header />
    <div class="heading" v-if="orders.all">
      Today's Pickup - {{ orders.all.count }}
      <button icon dark @click="getData">
        <i class="fa fa-refresh" />
      </button>
    </div>
    <div class="fx" v-if="orders.all">
      <h1 style="color:blue">{{ orders.all.total | currency }}</h1>
      <h1 style="color:red">&nbsp;- {{ orders.cancelled.total | currency }}</h1>
      <h1 style="color:green">
        &nbsp; = {{ (orders.all.total - orders.cancelled.total) | currency }}
      </h1>
    </div>
    <div class="content js-bt smallcard fx" v-for="c in chefs" :key="c._id._id">
      <nuxt-link :to="'/pickup/' + c._id._id">
        <div class="">
          <h2 class="text-3xl font-black">{{ c._id.restaurant }}</h2>
          <h3 class="text-2xl font-black">{{ c._id.qrno }}</h3>
          {{ c._id.phone }}
        </div>
        <div class="">
          <h1 class="text-3xl font-black text-orange-500">
            {{ c._id.vendor_name }}
          </h1>
        </div>
        <h1 class="text-3xl font-black text-red-500">
          {{ c.amount | currency }} ({{ c.count }})
        </h1>
      </nuxt-link>
    </div>
  </div>
</template>
<script>
const Header = () => import("~/components/Header");
// import io from "socket.io-client";
// import { WS_URL } from "~/config";
// let socket = io(WS_URL);
// import { SocketService } from "~/service/socket";
// let ss = new SocketService();
export default {
  data() {
    return {
      chefs: [],
      orders: {}
    };
  },
  created() {
    this.getData();
  },
  components: {
    Header
  },
  methods: {
    go(url) {
      this.$router.push(url);
    },
    async getData() {
      try {
        this.$store.commit("busy", true);
        this.chefs = await this.$axios.$get("api/food-orders/todaysChefs");
        // await ss.syncUpdates("user", this.chefs);
        this.orders = await this.$axios.$get("api/food-orders/todaysStatus");
        this.$store.commit("busy", false);
      } catch (e) {
        this.$store.commit("busy", false);
      } finally {
        this.$store.commit("busy", false);
      }
    }
  },
  head() {
    return {
      title: "Food items to be picked up"
    };
  }
};
</script>

<style scoped>
.heading {
  text-align: center;
  color: #fff;
  background: linear-gradient(87deg, #fb6340 0, #da1c5f 100%) !important;
  box-shadow: 0 0.3rem 0.3rem rgba(0, 0, 0, 0.175) !important;
  margin-bottom: 0.5rem;
  padding: 1rem 0;
  font-size: 2rem;
}
.amount {
  color: #da1c5f;
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
