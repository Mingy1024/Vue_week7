<template>
  <VLoading :active="isLoading" :z-index="1060"></VLoading>
  <table class="table mt-4">
    <thead>
      <tr>
        <th>購買時間</th>
        <th>Email</th>
        <th>購買款項</th>
        <th>應付金額</th>
        <th>是否付款</th>
        <th>編輯</th>
      </tr>
    </thead>
    <tbody>
      <template v-for="(item, key) in orders" :key="key">
        <tr v-if="orders.length" :class="{ 'text-secondary': !item.is_paid }">
          <td>{{ $filters.date(item.create_at) }}</td>
          <td><span v-text="item.user.email" v-if="item.user"></span></td>
          <td>
            <ul class="list-unstyled">
              <li v-for="(product, i) in item.products" :key="i">
                {{ product.product.title }} 數量：{{ product.qty }}
                {{ product.product.unit }}
              </li>
            </ul>
          </td>
          <td class="text-right">{{ item.total }}</td>
          <td>
            <div class="form-check form-switch">
              <input
                class="form-check-input"
                type="checkbox"
                :id="`paidSwitch${item.id}`"
                v-model="item.is_paid"
                @change="updateStatus(item)"
              />
              <label class="form-check-label" :for="`paidSwitch${item.id}`">
                <span v-if="item.is_paid">已付款</span>
                <span v-else>未付款</span>
              </label>
            </div>
          </td>
          <td>
            <div class="btn-group">
              <button
                class="btn btn-outline-primary btn-sm"
                type="button"
                @click="openModal(item)"
              >
                檢視
              </button>
              <button
                class="btn btn-outline-danger btn-sm"
                type="button"
                @click="openDelModal(item)"
              >
                刪除
              </button>
            </div>
          </td>
        </tr>
      </template>
    </tbody>
  </table>
  <Pagination :pages="pagination" @emitPages="getOrders"></Pagination>
  <OrderModal :order="tempOrder" ref="orderModal" @update-status="updateStatus"></OrderModal>
  <DelModal :item="tempOrder" ref="delModal" @del-item="delOrder"></DelModal>
</template>

<script>
import Pagination from '@/components/PaginationItem.vue'
import OrderModal from '@/components/OrderModal.vue'
import DelModal from '@/components/DelModal.vue'

export default {
  components: { Pagination, OrderModal, DelModal },
  data () {
    return {
      orders: {},
      tempOrder: {},
      isLoading: false,
      pagination: {},
      currentPage: 1
    }
  },
  methods: {
    getOrders (currentPage = 1) {
      this.currentPage = currentPage
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/orders?page=${currentPage}`
      this.isLoading = true
      this.$http
        .get(api)
        .then((res) => {
          console.log(res)
          this.orders = res.data.orders
          this.pagination = res.data.pagination
          this.isLoading = false
        })
        .catch((err) => {
          this.isLoading = false
          console.log(err)
        })
    },
    updateStatus (item) {
      this.isLoading = true
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${item.id}`
      const paid = {
        is_paid: item.is_paid
      }
      this.$http.put(api, { data: paid }).then((res) => {
        this.isLoading = false
        const orderModal = this.$refs.orderModal
        orderModal.hideModal()
        this.getOrders(this.currentPage)
        console.log(res.data.message)
      }).catch((err) => {
        console.log(err.data.message)
      })
    },
    delOrder () {
      this.isLoading = true
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${this.tempOrder.id}`
      this.$http.delete(api).then((res) => {
        this.isLoading = false
        const delModal = this.$refs.delModal
        delModal.hideModal()
        this.getOrders(this.currentPage)
        console.log(res.data.message)
      }).catch((err) => {
        console.log(err.data.message)
      })
    },
    openModal (item) {
      this.tempOrder = { ...item }
      const orderModal = this.$refs.orderModal
      orderModal.openModal()
    },
    openDelModal (item) {
      this.tempOrder = { ...item }
      const delModal = this.$refs.delModal
      delModal.openModal()
    }
  },
  mounted () {
    this.getOrders()
  }
}
</script>
