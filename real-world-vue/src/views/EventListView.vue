<script setup lang="ts">
// import TheWelcome from '../components/TheWelcome.vue'
// import { events } from '@/events';
import EventCard from '../components/EventCard.vue'
import type { EventItem } from '@/type'
// import { ref, type Ref } from 'vue'
import EventService from '@/services/EventService'
import NProgress from 'nprogress'
import { useRouter } from 'vue-router'
import type { AxiosResponse } from 'axios'
import { ref, type Ref, watchEffect, computed } from 'vue'
import router from '@/router'
import { onBeforeRouteUpdate } from 'vue-router'

const events: Ref<Array<EventItem>> = ref([])
const totalEvent = ref<number>(10)
const props = defineProps({
  page: {
    type: Number,
    required: true
  }
})
const pageSize = ref(3) //Defualt page size

EventService.getEvent(pageSize.value, props.page)
  .then((response: AxiosResponse<EventItem[]>) => {
    events.value = response.data
    totalEvent.value = response.headers['x-total-count']
  })
  .catch(() => {
    router.push({ name: 'NetworkError' })
  })
onBeforeRouteUpdate((to, from, next) => {
  const toPage = Number(to.query.page)
  EventService.getEvent(3, toPage)
    .then((response: AxiosResponse<EventItem[]>) => {
      events.value = response.data
      totalEvent.value = response.headers['x-total-count']
      next()
    })
    .catch(() => {
      next({ name: 'NextworkError' })
    })
})

const hasNextPage = computed(() => {
  const totalPages = Math.ceil(totalEvent.value / pageSize.value)
  // console.log("Total Pages: "+totalPages);
  // console.log("Total Event: "+totalEvent.value);
  return props.page.valueOf() < totalPages
})
</script>
<template>
  <h1>Event For Good</h1>
  <div class="pb-5">
    <label for="page-size">Page Size:</label>
    <input
      type="number"
      id="page-size"
      v-model="pageSize"
      @input="updatePage"
      min="1"
      :max="totalEvent"
    />
  </div>

  <main class="flex flex-col items-center">
    <EventCard v-for="event in events" :key="event.id" :event="event"></EventCard>
    <div class="flex w-72 justify-between">
      <RouterLink
        :to="{ name: 'event-list', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
        class="text-left text-gray-700 no-underline flex-1"
      >
        Prev Page
      </RouterLink>
      <RouterLink
        :to="{ name: 'event-list', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage"
        class="text-right text-gray-700 no-underline flex-1"
      >
        Next Page
      </RouterLink>
    </div>
  </main>
</template>