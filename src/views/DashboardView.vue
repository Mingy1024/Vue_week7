<template>
  <NavBar/>
  <router-view v-if="checkSuccess"></router-view>
</template>

<script>
import NavBar from '@/components/AdminNavbar.vue'

export default {
  components: { NavBar },
  data () {
    return {
      checkSuccess: false
    }
  },
  methods: {
    loginCheck () {
      const token = document.cookie.replace(
        /(?:(?:^|.*;\s*)Token\s*=\s*([^;]*).*$)|^.*$/,
        '$1'
      )
      if (token) {
        this.$http.defaults.headers.common.Authorization = `${token}`
        this.$http
          .post(`${process.env.VUE_APP_API}api/user/check`, {
            api_token: this.token
          })
          .then(() => {
            this.checkSuccess = true
          })
          .catch((err) => {
            alert(err.data.message)
            this.$router.push('/')
          })
      } else {
        alert('您尚未登入。')
        this.$router.push('/')
      }
    }
  },
  mounted () {
    this.loginCheck()
  }
}
</script>
