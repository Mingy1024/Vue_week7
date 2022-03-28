<template>
  <div class="container">
    <VLoading :active="isLoading" :z-index="1060"></VLoading>
    <div class="text-end mt-4">
      <button class="btn btn-primary" type="button" @click="openModal('new')">
        建立新的產品
      </button>
    </div>
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th>產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
          <th width="120">編輯</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td>{{ item.category }}</td>
          <td>{{ item.title }}</td>
          <td class="text-end">{{ item.origin_price }}</td>
          <td class="text-end">{{ item.price }}</td>
          <td>
            <span class="text-success" v-if="item.is_enabled">啟用</span>
            <span class="text-danger" v-else>未啟用</span>
          </td>
          <td>
            <div class="btn-group">
              <button
                type="button"
                class="btn btn-outline-primary btn-sm"
                @click="openModal('edit', item)"
              >
                編輯
              </button>
              <button
                type="button"
                class="btn btn-outline-danger btn-sm"
                @click="openModal('delete', item)"
              >
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <Pagination :pages="pagination" @emitPages="getData"></Pagination>
    <!-- ProductModal -->
    <ProductModal
      :isNew="isNew"
      :tempProduct="tempProduct"
      ref="ProductModal"
      @update-product = "updateProduct"
    ></ProductModal>
    <DelProductModal
      :item="tempProduct"
      ref="DelProductModal"
      @del-item = "delProduct"
    ></DelProductModal>
  </div>
</template>

<script>
import Pagination from '@/components/PaginationItem.vue'
import ProductModal from '@/components/ProductModal.vue'
import DelProductModal from '@/components/DelModal.vue'

export default {
  components: { Pagination, ProductModal, DelProductModal },
  data () {
    return {
      products: [],
      pagination: {},
      isNew: false,
      tempProduct: {},
      currentPage: 1,
      isLoading: false
    }
  },
  methods: {
    // 取得產品資料
    getData (page = 1) {
      this.currentPage = page
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/products?page=${page}`
      this.isLoading = true
      this.$http
        .get(api)
        .then((res) => {
          this.products = res.data.products
          this.pagination = res.data.pagination
          this.isLoading = false
        })
        .catch((err) => {
          this.isLoading = false
          alert(err.data.message)
        })
    },
    openModal (status, item) {
      if (status === 'new') {
        this.tempProduct = {
          imagesUrl: []
        }
        this.isNew = true
        this.$refs.ProductModal.openModal()
      } else if (status === 'edit') {
        this.tempProduct = JSON.parse(JSON.stringify(item)) // 深拷貝
        if (!this.tempProduct.imagesUrl) {
          // 如果 this.tempProduct.imagesUrl 不存在
          this.tempProduct.imagesUrl = []
        }
        this.isNew = false
        this.$refs.ProductModal.openModal()
      } else if (status === 'delete') {
        this.tempProduct = { ...item }
        // delProductModal.show()
        this.$refs.DelProductModal.openModal()
        console.log(this.tempProduct)
      }
    },
    updateProduct (item) {
      this.tempProduct = item
      let api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product`
      this.isLoading = true
      let httpMethod = 'post'
      if (!this.isNew) {
        api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${this.tempProduct.id}`
        httpMethod = 'put'
      }
      const ProductModal = this.$refs.ProductModal
      this.$http[httpMethod](api, { data: this.tempProduct }).then((res) => {
        this.isLoading = false
        console.log(res)
        ProductModal.hideModal()
        this.getData(this.currentPage)
      }).catch((err) => {
        this.isLoading = false
        console.log(err)
      })
    },
    delProduct () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${this.tempProduct.id}`
      this.isLoading = true
      this.$http.delete(api).then((res) => {
        this.isLoading = false
        console.log(res)
        const DelProductModal = this.$refs.DelProductModal
        DelProductModal.hideModal()
        this.getData(this.currentPage)
      }).catch((err) => {
        this.isLoading = false
        console.log(err)
      })
    }
  },
  mounted () {
    this.getData()
  }
}
</script>
