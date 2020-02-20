<template>
  <div>
    <Header />
    <div class="heading">
      Today's Delivery
      <button
        icon
        dark
        @click="getData"
      >
        <i class="fa fa-refresh" />
      </button>
    </div>
    <div
      class="flex justify-center text-2xl"
      v-if="orders.all"
    >
      <h1 class="text-blue-500">{{ orders.all.total | currency }}</h1>
      <h1 class="text-red-500">
        &nbsp;- {{ orders.cancelled.total | currency }}
      </h1>
      <h1 class="text-green-500">
        &nbsp; = {{ (orders.all.total - orders.cancelled.total) | currency }}
      </h1>
    </div>
    <div class="flex justify-center">
      <Radio
        v-model="status"
        value="pending"
        class="text-xs mr-2"
        v-if="orders.pending"
      >
        Pending ({{ orders.pending.count }})
      </Radio>
      <Radio
        v-model="status"
        value="od"
        class="text-xs mr-2"
        v-if="orders.od"
      >
        Out ({{ orders.od.count }})
      </Radio>
      <Radio
        v-model="status"
        value="delivered"
        class="text-xs mr-2"
        v-if="orders.delivered"
      >
        Delivered ({{ orders.delivered.count }})
      </Radio>
      <Radio
        v-model="status"
        value="cancelled"
        class="text-xs mr-2"
        v-if="orders.cancelled"
      >
        Cancelled ({{ orders.cancelled.count }})
      </Radio>
      <Radio
        v-model="status"
        value="all"
        class="text-xs mr-2"
        v-if="orders.all"
      >
        All ({{ orders.all.count }})
      </Radio>
    </div>
    <div v-if="orders[status]">
      <div
        class="flex flex-col justify-between smallcard"
        v-for="f in orders[status].items"
        :key="f._id"
      >
        <div
          class="flex justify-between border-b pt-1"
          @click="go('/delivery/' + f._id)"
        >
          <div class="">
            <h2 class="text-3xl">{{ f.vendor.qrno }}</h2>
            <div class="font-bold">{{ f.vendor.phone }}</div>
            <div class="text-gray-800">{{ f.vendor.restaurant }}</div>
          </div>
          <div class="">
            <h1 class="text-3xl">{{ f.address.qrno }}</h1>
            <div class="font-bold">{{ f.phone }}</div>
            <div class="text-gray-800">{{ f.address.recipient_name }}</div>
          </div>
          <div class="font-bold text-green-500 text-3xl">
            {{ f.amount | currency }}
          </div>
        </div>
        <div class="bg-yellow-100">{{ f.item.name }}</div>
      </div>
    </div>
    <StickyFooter />
  </div>
</template>
<script>
const Header = () => import("~/components/Header");
const Radio = () => import("~/components/ui/Radio");
const StickyFooter = () => import("~/components/footer/StickyFooter");
// import io from "socket.io-client";
// import { WS_URL } from "~/config";
// let socket = io(WS_URL);
// import { SocketService } from "~/service/socket";
// let ss = new SocketService();
export default {
  data() {
    return {
      status: "pending",
      orders: {}
    };
  },
  created() {
    this.getData();
  },
  components: { Header, Radio, StickyFooter },
  methods: {
    go(url) {
      this.$router.push(url);
    },
    async getData() {
      try {
        this.$store.commit("busy", true);
        this.orders = await this.$axios.$get("api/food-orders/todaysStatus");
      } catch (e) {
      } finally {
        this.$store.commit("busy", false);
      }
    }
  }
};
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
