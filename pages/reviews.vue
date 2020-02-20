<template>
  <div>
    <div class="heading">Reviews
      <button>
        <i class="fa fa-refresh" />
      </button>
    </div>
    <StickyFooter />
  </div>
</template>
<script>
const StickyFooter = () => import("~/components/footer/StickyFooter");

export default {
  data() {
    return {
      order: {}
    };
  },
  async created() {},
  components: {
    StickyFooter
  }
};
</script>

