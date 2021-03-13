<template>
  <div class="datepicker-container">
    <div class="from-container">
      <label for="from">From:</label>
      <input type="date" id="from" name="from" v-model="startDate">
    </div>
    <div class="to-container">
      <label for="to">To:</label>
      <input type="date" id="to" name="to" v-model="endDate">
    </div>
    <button v-on:click="$emit('changeDates', { start: startDate, end: endDate })"> Go </button>
  </div>
</template>

<script>
import '@/assets/styles/DatePicker.scss'
export default {
  name: 'DatePicker',
  props: ['defaultDates'], // earliest and latest dates received from the API are used on first load
  data () {
    return {
      startDate: this.defaultDates.start,
      endDate: this.defaultDates.end
    }
  }
}
</script>
