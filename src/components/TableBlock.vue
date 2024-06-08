<template>
  <div class="table-wrapper">
    <input class="table-search" v-model="searchText" type="text" placeholder="Введите поисковый запрос">
    <div v-if="!filteredRows.length" class="table-info">Ничего не найдено</div>
    <table v-if="filteredRows.length" class="table">
      <thead>
        <tr>
          <td>Аватар</td>
          <td>ФИО</td>
          <td>Пол</td>
          <td>Страна</td>
          <td>Дата рождения</td>
          <td>Адрес электронной почты</td>
          <td>Телефон</td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in pageRows" :key="row.login.uuid">
          <td><img :src="row.picture.medium" /></td>
          <td>{{ `${row.name.title} ${row.name.last} ${row.name.first}`}}</td>
          <td>{{ row.gender }}</td>
          <td>{{ row.location.country }}</td>
          <td>{{ getDOB(row.dob.date) }}</td>
          <td>{{ row.email }}</td>
          <td>{{ row.phone }}</td>
        </tr>
      </tbody>
    </table>
    <template v-if="filteredRows.length">
      <PaginationBlock
        :rows-quantity="filteredRows.length"
        :rows-per-page="ROWS_PER_PAGE"
        :current-page="currentPage"
        @set-current-page="setCurrentPage"
      />
    </template>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import api from '../api.json'
import PaginationBlock from '../components/PaginationBlock.vue'
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

const ROWS_PER_PAGE = 20
const searchText = ref(route.query.search as string || '')

const allRows = computed(() => api.results)

const filteredRows = computed(() => {
  const formattedSearchText = getFormatText(searchText.value)
  return formattedSearchText ? api.results.filter(row => {
    return getFormatText(row.name.last).includes(formattedSearchText) ||
          getFormatText(row.name.first).includes(formattedSearchText) ||
          getFormatText(row.gender).includes(formattedSearchText) ||
          getFormatText(row.location.country).includes(formattedSearchText) ||
          getFormatText(row.dob.date).includes(formattedSearchText) ||
          getFormatText(row.email).includes(formattedSearchText) ||
          getFormatText(row.phone).includes(formattedSearchText)
  }) : allRows.value
})

const currentPage = ref(Number(route.query.page) || 1)
const pageRows = computed(() => getPageRows(currentPage.value))

type TTableQuery = {
  page?: number
  search?: string
}
watch(() => searchText.value, async (val) => {
  currentPage.value = 1
  const query: TTableQuery  = { page: 1 }
  if (val) query.search = val
  router.push({ name: 'table', query })
})

function getPageRows(currentPage: number) {
  return filteredRows.value.slice((currentPage - 1) * ROWS_PER_PAGE, (currentPage - 1) * ROWS_PER_PAGE + ROWS_PER_PAGE)
}

function getDOB (date: string) {
  return new Date(date).toLocaleString("en-US", {
    year: "numeric",
    month: "long",
    day: "numeric"
  })
}

function getFormatText (text: string) {
  return text.toLowerCase().trim()
}

function setCurrentPage (page: number) {
  currentPage.value = page
  router.push({ name: 'table', query: { ...route.query, page }})
}

</script>

<style scoped>
.table {
  border: 1px solid #000;
  border-collapse: collapse;
  margin: 0 auto 25px;
  width: 100%;
  min-height: 2050px;
}
.table thead td {
  font-weight: bold;
  font-size: 18px;
  text-align: center;
}
.table td {
  border: 1px solid #000;
  padding: 10px;
}
.table-search {
  margin-bottom: 15px;
  padding: 10px;
  width: 100%;
  max-width: 400px;
}
.table-info {
  font-weight: bold;
  font-size: 20px;
}
</style>