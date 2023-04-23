<template>
  <AppHeader />

  <router-view></router-view>

  <AppPlayer />

  <Auth />
</template>
<script>
import AppHeader from './components/Header.vue'
import Auth from './components/Auth.vue'
import { mapWritableState } from 'pinia'
import useUserStore from '@/stores/user'
import { auth } from './includes/firebase'
import AppPlayer from '@/components/Player.vue'

export default {
  name: 'App',
  components: {
    AppPlayer,
    AppHeader,
    Auth
  },
  computed: {
    ...mapWritableState(useUserStore, ['userLoggedIn'])
  },
  created() {
    if (auth.currentUser) {
      this.userLoggedIn = true
    }
  }
}
</script>
