<template>
  <div>
    <label for="from">From:</label>
    <input type="date" id="from" name="from" v-model="startDate">
    <label for="to">To:</label>
    <input type="date" id="to" name="to" v-model="endDate">
    <button v-on:click="$emit('changeDates', { start: startDate, end: endDate })"> Go </button>
  </div>
</template>

<script>
import '@/assets/styles/DatePicker.scss'
export default {
  name: 'DatePicker',
  props: ['defaultDates'],
  data () {
    return {
      startDate: this.defaultDates.start,
      endDate: this.defaultDates.end
    }
  },
  created () {
    // console.log(this.defaultDates)
  }
}
</script>
