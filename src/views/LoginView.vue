<template>
  <div class="container mt-5">
    <VLoading :active="isLoading" :z-index="1060"></VLoading>
    <!-- <ToastMessages></ToastMessages> -->
    <form class="row justify-content-center" @submit.prevent="login">
      <div class="col-md-6">
        <h1 class="h3 mb-3 font-weight-normal">請先登入</h1>
        <div class="mb-2">
          <label for="inputEmail" class="sr-only">Email address</label>
          <input
            type="email"
            id="inputEmail"
            class="form-control"
            placeholder="Email address"
            v-model="user.username"
            required
            autofocus
          />
        </div>
        <div class="mb-2">
          <label for="inputPassword" class="sr-only">Password</label>
          <input
            type="password"
            id="inputPassword"
            class="form-control"
            v-model="user.password"
            placeholder="Password"
            required
          />
        </div>
        <div class="text-end mt-4">
          <button class="btn btn-lg btn-primary btn-block" type="submit">
            登入
          </button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  data () {
    return {
      user: {},
      isLoading: false
    }
  },
  methods: {
    login () {
      const api = `${process.env.VUE_APP_API}admin/signin`
      this.isLoading = true
      this.$http
        .post(api, this.user)
        .then((res) => {
          this.isLoading = false
          const { token, expired } = res.data
          document.cookie = `Token=${token};expires=${new Date(expired)};`
          this.$router.push('/admin/products')
        })
        .catch((err) => {
          this.isLoading = false
          console.log(err.response.data.message)
        })
    }
  }
}
</script>
