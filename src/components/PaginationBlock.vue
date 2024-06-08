<template>
  <div class="pagination">
    <div class="pagination__base">
      <button
        class="pagination__item pagination__item-back"
        @click="$emit('set-current-page', currentPage - 1)"
        :disabled="currentPage === 1"
      >
        &lt;
      </button>
      <button
        class="pagination__item pagination__item-forward"
        @click="$emit('set-current-page', currentPage + 1)"
        :disabled="currentPage === paginationItemsNum"
      >
        &gt;
      </button>
    </div>
    <div class="pagination__pages">
      <button
        v-for="item in paginationItems"
        class="pagination__item"
        :class="{ 'pagination__item--active': currentPage === item}"
        :key="item"
        @click="$emit('set-current-page', item)"
      >
        {{ item }}
      </button>
    </div>
  </div>
</template>


<script setup lang="ts">
import { defineProps, computed } from 'vue'

defineEmits([ 'set-current-page' ])


interface IProps {
  rowsQuantity: number
  rowsPerPage: number
  currentPage: number
}

const props = defineProps<IProps>()

const paginationItemsNum = computed(() => Math.ceil(props.rowsQuantity / props.rowsPerPage))

const paginationItems = computed(() => Array.from({ length: paginationItemsNum.value }, (_, i) => i + 1))

</script>

<style scoped>
.pagination__base,
.pagination__pages {
  display: flex;
  gap: 8px;
  overflow: auto;
  padding: 5px 0;
}
.pagination__item {
  border: 1px dashed #000;
  padding: 5px 8px;
  background: #fff;
  min-height: 30px;
  cursor: pointer;
  font-weight: bold;
}
.pagination__item:hover {
  background: grey;
  color: #fff;
}

.pagination__item[disabled],
.pagination__item--active {
  pointer-events: none;
}

.pagination__item--active {
  background: grey;
  color: #fff;
}
</style>
