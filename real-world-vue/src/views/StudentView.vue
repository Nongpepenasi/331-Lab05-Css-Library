<script setup lang="ts">
import Student from '../components/Student.vue'
// import { events } from '@/events';
import type { StudentItem } from '@/type'
import { ref, type Ref} from 'vue'
// import EventService from '@/services/EventService'
import StudentService from '@/services/StudentService'

const students: Ref<Array<StudentItem>> = ref([])
  
  StudentService.getStudent().then((response) => {
    students.value = response.data
    console.log(students.value)
  })
</script>

<template>
  <div class="flex flex-col items-center text-right">
    <h1>This is a students page</h1>
    <!-- <Category></Category> -->
    <div class="flex flex-col items-center text-right">
      <Student v-for="student in students" :key="student.id" :student="student"></Student>
    </div>
  </div>
</template>

<style>

</style>
