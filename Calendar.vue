<template>
<div class="p-4">
    <h1 class="text-2xl font-bold mb-4 text-center">Calendar</h1>
    <div class="flex items-center justify-center mb-4">
    <button @click="prevMonth" class="px-4 py-2 rounded">Prev</button>
    <h2 class="mx-4 text-xl font-bold">{{ monthName }} {{ selectedYear }}</h2>
    <button @click="nextMonth" class="px-4 py-2 rounded">Next</button>
    </div>
    <div class="grid grid-cols-7 gap-4 mb-4">
    <div class="font-bold text-center">Sun</div>
    <div class="font-bold text-center">Mon</div>
    <div class="font-bold text-center">Tue</div>
    <div class="font-bold text-center">Wed</div>
    <div class="font-bold text-center">Thu</div>
    <div class="font-bold text-center">Fri</div>
    <div class="font-bold text-center">Sat</div>

    <div v-for="(day, index) in daysInMonth" :key="index" class="p-2 border text-center rounded hover:bg-gray-200">
        <div @click="selectDay(day)" class="cursor-pointer">{{ day }}</div>
        <div v-if="schedules[`${selectedYear}-${selectedMonth + 1}-${day}`]" class="text-sm">
        {{ schedules[`${selectedYear}-${selectedMonth + 1}-${day}`].time }} - {{ schedules[`${selectedYear}-${selectedMonth + 1}-${day}`].description }}
        </div>
    </div>
    </div>

    <div v-if="selectedDay" class="mt-4">
    <h2 class="text-lg font-bold">Add Schedule for {{ selectedDay }} {{ selectedMonth + 1 }} {{ selectedYear }}</h2>
    <input v-model="scheduleTime" type="time" class="border rounded p-2" />
    <input v-model="scheduleDescription" type="text" placeholder="Description" class="border rounded p-2 ml-2" />
    <button @click="addSchedule" class="ml-2 px-4 py-2 rounded">Add</button>
    </div>
</div>
</template>
  
  <script setup>
  import { ref, computed } from 'vue';
  
  const selectedYear = ref(new Date().getFullYear());
  const selectedMonth = ref(new Date().getMonth());
  const selectedDay = ref(null);
  const scheduleTime = ref('');
  const scheduleDescription = ref('');
  const schedules = ref({});
  
  const months = [
    'January', 'February', 'March', 'April', 'May', 'June',
    'July', 'August', 'September', 'October', 'November', 'December'
  ];
  
  const monthName = computed(() => months[selectedMonth.value]);
  
  const daysInMonth = computed(() => {
    const date = new Date(selectedYear.value, selectedMonth.value + 1, 0);
    return Array.from({ length: date.getDate() }, (_, i) => i + 1);
  });
  
  function selectDay(day) {
    selectedDay.value = day;
  }
  
  function addSchedule() {
    if (scheduleTime.value && scheduleDescription.value) {
      schedules.value[`${selectedYear.value}-${selectedMonth.value + 1}-${selectedDay.value}`] = {
        time: scheduleTime.value,
        description: scheduleDescription.value,
      };
      scheduleTime.value = '';
      scheduleDescription.value = '';
    }
  }
  
  function prevMonth() {
    if (selectedMonth.value === 0) {
      selectedMonth.value = 11;
      selectedYear.value--;
    } else {
      selectedMonth.value--;
    }
  }
  
  function nextMonth() {
    if (selectedMonth.value === 11) {
      selectedMonth.value = 0;
      selectedYear.value++;
    } else {
      selectedMonth.value++;
    }
  }
  </script>
  
  <style scoped>
  /* Additional styles can be added here */
  </style>
  