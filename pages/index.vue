<template>
  <div>
    <div class="h-screen">
      <Heading title="Welcome to misiki" />
      <div v-if="orders">
        <div
          class="heading"
          v-if="orders.all"
        >
          Today's Pickup - {{ orders.all.count }}
          <button
            icon
            dark
            @click="getData"
          >
            <i class="fa fa-refresh" />
          </button>
        </div>
        <div
          class="fx"
          v-if="orders.all"
        >
          <h1 style="color:blue">{{ orders.all.total | currency }}</h1>
          <h1 style="color:red">&nbsp;- {{ orders.cancelled.total | currency }}</h1>
          <h1 style="color:green">
            &nbsp; = {{ (orders.all.total - orders.cancelled.total) | currency }}
          </h1>
        </div>
      </div>

    </div>
    <StickyFooter />
    <GeoLocation />
  </div>
</template>

<script>
import GeoLocation from "~/components/GeoLocation.vue";
import Heading from "~/components/Heading.vue";
import StickyFooter from "~/components/footer/StickyFooter";

export default {
  fetch({ store, redirect }) {
    if (!(store.state.auth || {}).user)
      return redirect("/login?return=my/profile");
  },
  created() {
    this.getData();
  },
  async asyncData({ $axios }) {
    let orders = [],
      status = "Received",
      todayTotal = null,
      todaySummary = null;
    try {
      orders = await $axios.$get("api/food-orders/pending");
      todayTotal = await $axios.$get("api/food-orders/my/today");
      todaySummary = await $axios.$get("api/food-orders/todays-summary");
    } catch (e) {}
    return { orders: orders.data, todayTotal, todaySummary };
  },
  components: {
    GeoLocation,
    Heading,
    StickyFooter
  },
  methods: {
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
  }
};
</script>
