<template>
  <div>
    <Header />
    <div class="heading">Delivery Details</div>
    <div class="product-card">
      <div class="column fx">
        <div class="card-container">
          <div class="a-contain pl-0-5">
            <div><img
                class="img-size"
                src="/shop1.png"
              /> </div>
            <div
              class="column"
              v-if="order.vendor"
            >
              <div class="text-bold ">{{order.vendor.restaurant}}</div>
              <div class="a-contain pl-0-5">
                <div class="pt-img"><img
                    class="img-loacte"
                    src="/blocate.png"
                  /></div>
                <div class="text-small">{{order.vendor.phone}}</div>
              </div>
            </div>
          </div>
          <div class="dotted">
          </div>
          <div class="a-contain pl-0-5">
            <div><img
                class="img-size border"
                src="/person2.png"
              /> </div>
            <div
              class="column"
              v-if="order.address"
            >
              <div class="text-bold ">{{order.address.recipient_name}}</div>
              <div class="a-contain pl-0-5">
                <div class="pt-img">
                  <img
                    class="img-loacte"
                    src="/bulocate.png"
                  />
                </div>
                <div class="text-small">{{order.address.qrno}}</div>
              </div>
            </div>
          </div>
        </div>
        <h3>{{order.rate | currency}} * {{order.qty}} = {{order.amount | currency}}</h3>
      </div>
    </div>
    <div
      class="card"
      v-if="order.item"
    >
      <div class="row">
        <div class="img-align">
          <img
            class="cartimg-size"
            :src="order.item.img"
          />
        </div>
        <div class="column w-6 pleft-1 ptop-1">
          <div class="text-style">{{order.item.name}}</div>
          <div class=" text-style price">{{order.rate | currency}} x {{order.qty}}</div>
        </div>
        <!-- <div class="cross-align">
           <img class="cross" src="close.png"/>
            </div> -->
      </div>
    </div>
    <!-- <div> <h3>Payment Method</h3></div>
               <div class="b-card ">
                   <div class="a-contain">
                       <div class="pl-0-5">
                          <img class="img-size" src="atm.png"/> 
                     </div>
                     <span class="span-text">Credit/Debit Card</span>
                   </div>
                    </div>-->
    <div class="b-card ">
      <div class="a-contain">
        <div class="pl-0-5">
          <img
            class="img-size"
            src="cash.png"
          />
        </div>
        <span class="span-text">To Collect <h1>{{order.amount | currency}}</h1></span>
      </div>
    </div>
    <!-- <div class="b-card ">
                   <div class="a-contain">
                       <div class="pl-0-5">
                          <img class="img-size" src="paypal.png"/> 
                     </div>
                     <span class="span-text">Paypal</span>
                   </div>
                    </div> -->
    <div class="pt-1 center pb-1"> <button class="button-lg">
        <span style="font-size: 1rem">Delivered</span>
      </button> </div>
    <div class="pt-1 center pb-1"> <button>
        <span style="font-size: 1rem">Cancel</span>
      </button> </div>
    <div class="btn-align footer">
      <div class="btn btn-block btn-white back-to-menu-btn">
        <div>
          <img
            class="height35"
            src="/backarrow.svg"
          />
        </div>
        <div>
          <router-link
            to="/my/food/delivery"
            class="btnclr"
          >Back to all Delivery</router-link>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
const Header = () => import("~/components/Header");
export default {
  async asyncData({ $axios, route }) {
    let order = {};
    try {
      order = await $axios.$get("api/food-orders/" + route.params.id);
      return { order };
    } catch (e) {
      order = {};
    }
  },
  components: { Header }
};
</script>

<style scoped>
.btn {
  padding: 7px;
  font-size: 1.25rem;
  line-height: 1.5;
  border-radius: 0.3rem;
}
.btn-white {
  background: linear-gradient(87deg, #fb6340 0, #da1c5f 100%) !important;
  border-color: #fff;
  -webkit-box-shadow: 0 4px 6px rgba(50, 50, 93, 0.11),
    0 1px 3px rgba(0, 0, 0, 0.08);
  box-shadow: 0 4px 6px rgba(50, 50, 93, 0.11), 0 1px 3px rgba(0, 0, 0, 0.08);
}
.back-to-menu-btn {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
h1 {
  margin-left: 2rem;
}
h2 {
  text-shadow: 1px 1px #3c3333;
  font-size: 1.2rem;
  padding-left: 1rem;
  text-align: center;
  padding-top: 1rem;
}
h3 {
  text-shadow: 1px 1px #3c3333;
  font-size: 1.2rem;
  padding-left: 1rem;
}
.product-card {
  display: flex;
  background-color: rgb(247, 247, 247);

  border-radius: 0.2rem;
  margin: 0.5rem;
  box-shadow: 0 0.1rem 0.1rem rgba(0, 0, 0, 0.175) !important;
  /* width: calc(50% - 1rem); */
}
@media (min-width: 650px) {
  .product-card {
    width: calc(25% - 1rem);
  }
}
@media (min-width: 1000px) {
  .product-card {
    width: calc(20% - 1rem);
  }
}
.backgroundimg {
  border-radius: 0.3rem 0.3rem 0 0;
  min-height: 126px;
  /* box-shadow: 0 0.1rem 0.1rem rgba(0, 0, 0, 0.175) !important; */
  background-size: cover;
  background-attachment: fixed;
  position: relative;
  width: 100%;
  height: 20vh;
}
.pt-img {
  padding-top: 0.6rem;
  padding-left: 0.5rem;
}

.img-size {
  width: 3rem;
  height: 3rem;
}
.a-contain {
  display: flex;
  flex-direction: row;
}

/* .align-row {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
} */
/* @media (min-width:300px) and (max-width:800px ) {
     .align-row{
        display: flex;
    flex-direction: row;
    } 
    } */

.text-bold {
  font-size: 1.2rem;
  /* letter-spacing: 1px; */
  padding-left: 1rem;
  font-weight: 700;
}
.text-small {
  font-size: 0.8rem;
  /* letter-spacing: 1px; */
  padding-top: 0.8rem;
  font-weight: 700;
  color: grey;
  padding-left: 0.2rem;
}
.card-container {
  padding: 0.4rem;
}
.dotted {
  border-left: 3px dotted #b7b6b6;
  /* -webkit-transform: rotate(90deg); */
  transform: rotate(180deg);
  width: 4rem;
  padding-top: 3rem;
  margin-top: 0rem;
  margin-left: -2rem;
}
.img-loacte {
  width: 1rem;
  height: 1rem;
}
.b-card {
  padding: 0.5rem;
  box-shadow: 0 -0.1rem 1.1rem rgba(0, 0, 0, 0.175) !important;
  border-radius: 0.5rem;
  height: 4rem;
  margin: 1rem;
}
.span-text {
  padding-left: 1.5rem;
  display: flex;
  align-items: center;
  font-size: 1.1rem;
  font-weight: 700;
}
.button-lg {
  display: flex;
  justify-content: center;
  align-items: center;
  text-transform: none;
  color: #fff;
  box-shadow: 0 4px 6px rgba(50, 50, 93, 0.11), 0 1px 3px rgba(0, 0, 0, 0.08);
  font-size: 1.5rem;
  font-weight: 700;
  border-radius: 0.3rem;
  border: none;
  padding: 0.9rem 6rem;
  outline: none;
  background: linear-gradient(87deg, #4068fb 0, #1ad0de 100%) !important;
  border-color: #69beda;
}
.card {
  padding: 0.5rem;
  -webkit-box-shadow: 0 -0.1rem 1.1rem rgba(0, 0, 0, 0.175) !important;
  box-shadow: 0 -0.1rem 1.1rem rgba(0, 0, 0, 0.175) !important;
  margin-top: 0.5rem;
  align-items: center;
  border-radius: 0.5rem;
  margin-left: 0.5rem;
  margin-right: 0.5rem;
}
.cartimg-size {
  width: 6rem;
  height: 6rem;
}
.img-align {
  text-align: center;
  width: 27%;
}
.ptop-1 {
  padding-top: 0.5rem;
}
.pleft-1 {
  padding-left: 1rem;
}
.w-6 {
  width: 60%;
}
.text-style {
  font-size: 1.2rem;
  font-weight: 500;
}
.price {
  padding-top: 0.6rem;
}
.border {
  border: 2px solid #2b73cc;
  border-radius: 2rem;
}
</style>
