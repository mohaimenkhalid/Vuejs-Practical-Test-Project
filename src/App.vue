<template>
  <Navbar />
  <div class="container min-vh-100">
    <router-view />
  </div>
  <Footer />
  <Toast />
</template>
<script>
import { mapState } from "vuex"
import Navbar from "@/components/partials/Navbar";
import Footer from "@/components/partials/Footer";
import './assets/placeholder-loading.min.css'
export default {
  name: 'App',
  components: {
    Footer,
    Navbar
  },
  computed: {
    ...mapState("account", ["name"]),
  }
}
</script>

<style>
</style>
