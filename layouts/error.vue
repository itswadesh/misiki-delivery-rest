<template>
  <div
    class="flex flex-col justify-center items-center text-lg antialiased"
    style="min-height:71.8vh"
  >
    <Loading />
    <div v-if="$store.state.errors" class="err">
      <span v-for="(e,ix) in $store.state.errors" :key="ix">{{e.message}}</span>
    </div>
    <h1 v-if="error.statusCode === 404">Not found</h1>
    <pre v-else>{{error}}</pre>&nbsp;
    <nuxt-link to="/" class="primary text-2xl px-4 rounded">Home page</nuxt-link>
  </div>
</template>

<script>
import Loading from '~/components/ui/Loading'

export default {
  props: ['error'],
  components: { Loading }
}
</script>
