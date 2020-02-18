<template>
  <div>
    <Header />
    <div class="heading">
      <nuxt-link to="/pickup">
        <i class="fa fa-long-arrow-left" />
      </nuxt-link>
      <span v-if="orders[0]"> {{ orders[0].vendor.restaurant }}</span> Pickup
      <button @click="getData">
        <i class="fa fa-refresh" />
      </button>
    </div>

    <div class="flex" v-if="total">
      <h1 style="color:blue">{{ total.total | currency }}</h1>
    </div>
    <div
      class="flex flex-col justify-between smallcard"
      v-for="f in orders"
      :key="f._id"
    >
      <div
        class="flex justify-between items-center border-b pt-1"
        @click="go('/delivery/' + f._id)"
      >
        <div class="">
          <h2 class="text-3xl font-black">{{ f.vendor.qrno }}</h2>
          {{ f.vendor.phone }}
        </div>
        ->
        <div class="">
          <h1 class="text-3xl font-black">{{ f.address.qrno }}</h1>
          {{ f.phone }}
        </div>
        <div class=" font-black text-green-600 text-3xl">
          {{ f.amount | currency }}
        </div>
      </div>
      <div class=" bg-yellow-200 text-black">{{ f.item.name }}</div>
      <div class="flex items-center justify-between">
        <button class="button" @click="save(f._id, 'Out For Delivery')">
          Pick
        </button>
        <button @click="cancel(f._id, 'Cancelled')">Cancel</button>
      </div>
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
      orders: [],
      total: {},
      restaurant: null
    };
  },
  created() {
    this.getData();
  },
  components: {
    Header
  },
  methods: {
    cancel(id, status) {
      this.$swal({
        title: "Are you sure to cancel?",
        type: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, Cancel!"
      }).then(async result => {
        if (result.value) {
          this.save(id, status);
        }
      });
    },
    async save(id, status) {
      try {
        this.$store.commit("busy", true);
        await this.$axios.$put("api/food-orders/" + id, { status });
        this.getData();
        // this.$router.push("/food/pickup");
      } catch (e) {
      } finally {
        this.$store.commit("busy", false);
      }
    },
    go(url) {
      this.$router.push(url);
    },
    async getData() {
      try {
        this.$store.commit("busy", true);
        this.orders = await this.$axios.$get(
          "api/food-orders/chef/" + this.$route.params.id
        );
        // await ss.syncUpdates("food-order", this.orders);
      } catch (e) {
      } finally {
        this.$store.commit("busy", false);
      }
    }
  }
};
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
