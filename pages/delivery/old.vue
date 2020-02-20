<template>
  <div>
    <Header />
    <div class="heading">Old Delivery</div>
    <div>
      <ul class="p-left">
        <li
          class="card"
          v-for="(o,ix) in orders"
          :key="ix"
        >
          <div class="pl-1">
            <h1 class="seller">{{o._id.restaurant}} - {{o._id.phone}}</h1>
          </div>
          <div
            v-for="(i,ixx) in o.data"
            :key="'i-'+ixx"
            class="fx customer"
          >
            <div style="color:#333">{{i.item}}</div>
            <div class="pt-1">{{i.name}} ({{i.phone}})</div>
            <div class="pt-1">{{i.qty}} * {{i.rate | currency}} = {{i.amount | currency}}</div>
            <div style="color:red">{{i.qrno}}</div>
            <div style="color:violet;font-size:0.7rem;text-align:center">{{i.createdAt | date}}</div>
          </div>
          <div class="center pt-1">
            <Button
              v-model="o.status"
              value="Cancelled"
              class="btn4 font"
            >
              Cancelled
            </Button>
            <Button
              v-model="o.status"
              value="Prepared"
              class="btn1 font"
            >
              Prepared
            </Button>
            <Button
              v-model="o.status"
              value="Out For Delivery"
              class="btn2 font"
            >
              Out For Delivery
            </Button>
            <Button
              v-model="o.status"
              value="Delivered"
              class="btn3 font"
            >
              Delivered
            </Button>
          </div>
        </li>
      </ul>
    </div>
    <nuxt-link
      to="/delivery/"
      class="history-button"
    >Today's Delivery</nuxt-link>
  </div>
</template>
<script>
const Header = () => import("~/components/Header");
const Button = () => import("~/components/ui/Button");
export default {
  async asyncData({ $axios }) {
    let orders = [],
      status = "Received";
    try {
      orders = await $axios.$get("api/food-orders/old-group");
    } catch (e) {}
    return { orders };
  },
  components: { Header, Button },
  methods: {
    async save(o) {
      try {
        this.$store.commit("busy", true);

        await this.$axios.$put("api/food-orders/" + o._id, {
          status: o.status
        });
      } catch (e) {
      } finally {
        this.$store.commit("busy", false);
      }
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
.center {
  text-align: center;
  padding-top: 1rem;
}

.font {
  font-size: 0.7rem;
  padding-left: 0.5rem !important;
  font-weight: 900;
}
</style>

