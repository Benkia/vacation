
<script lang="ts">
import { HebrewCalendar } from '@hebcal/core';
import { defineComponent } from 'vue'

export default defineComponent({

  data() {
    return {
      startDate: Date.now(),
      endDate: '',
      workdays: 0
    }
  },
  methods: {
    calcHolidays(start: Date, end: Date): number {

      // Get holidays between start and end dates
      var cal = HebrewCalendar.calendar({ start: start, end: end })
      var numOfHolidays = 0
      cal.forEach(element => {

        console.log(element.getDesc())
        console.log(element.getDate().greg())
        console.log(this.isWOrkingDay(element.getDate().getDay()))
        if (element.getDesc() === "Erev Pesach") {
          numOfHolidays += 0.5
        }
      });

      return numOfHolidays
    },
    async calcWorkingDays() {
      // Validate input
      if (!this.startDate || !this.endDate) {
        alert('Please enter a start date and an end date ');
        return;
      }

      // Parse dates
      const start = new Date(this.startDate);
      const end = new Date(this.endDate);
      if (isNaN(start.getTime()) || isNaN(end.getTime())) {
        alert('Invalid date format');
        return;
      }
      var holidays = this.calcHolidays(start, end)
      var currentDate = start
      var numOfDays = 0

      while (currentDate <= end) {

        // Check if the current day is a weekday (Monday through Friday)
        if (this.isWOrkingDay(currentDate.getDay())) {
          numOfDays++;
        }

        // Increment the current date by one day
        currentDate.setDate(currentDate.getDate() + 1);
      }

      this.workdays = numOfDays - holidays

    },
    isWOrkingDay(day: number): boolean { return day == 6 || day < 4 }
  }


})
</script>



<template>
  <div>
    <label>Start Date:</label>
    <input type="Date" v-model="startDate"  />
    <br />
    <label>End Date:</label>
    <input type="Date" v-model="endDate" />
    <br />
    <button @click="calcWorkingDays()">Check workdays</button>
    <p v-if="workdays">There are {{ workdays }} working days between {{ startDate }} and {{ endDate
    }}:</p>
  </div>
</template>
