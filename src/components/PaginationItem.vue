<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination justify-content-center">
      <!-- 判斷有無前一頁，無的話將按鈕disabled -->
      <li :class="{ disabled: !pages.has_pre }" class="page-item">
        <a
          class="page-link"
          href="#"
          aria-label="Previous"
          @click.prevent="togglePage(pages.current_page - 1)"
        >
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>
      <!-- 在當前頁面顯示 active 效果 -->
      <li
        class="page-item"
        v-for="page in pages.total_pages"
        :key="page"
        :class="{ active: pages.current_page === page }"
      >
        <span class="page-link" v-if="page === pages.current_page">{{
          page
        }}</span>
        <a
          class="page-link"
          v-else
          href="#"
          @click.prevent="togglePage(page)"
          >{{ page }}</a
        >
      </li>
      <!-- 判斷有無下一頁，無的話將按鈕disabled -->
      <li class="page-item" :class="{ disabled: !pages.has_next }">
        <a
          class="page-link"
          href="#"
          aria-label="Next"
          @click.prevent="togglePage(pages.current_page + 1)"
        >
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  props: ['pages'],
  methods: {
    togglePage (page) {
      this.$emit('emitPages', page)
    }
  }
}
</script>
