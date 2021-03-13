<template>
  <div id="app">
    <div v-if="!loading" >
      <ShowMessage :message="currentFiresData.length" :title="title"/>
      <DatePicker v-bind:defaultDates="{ start: currentFiresData[0].date, end: currentFiresData[currentFiresData.length - 1].date }"
      v-on:changeDates="handleDateChange"
      />
      <MapComponent v-bind:firesData="currentFiresData"/>
    </div>
    <img src="@/assets/img/loading.svg" alt="Loading Spinner" v-if="loading">
  </div>
</template>

<script>
import axios from 'axios'
import MapComponent from './components/MapComponent.vue'
import DatePicker from './components/DatePicker.vue'
import ShowMessage from './components/ShowMessage.vue'
import '@/assets/styles/App.scss'

export default {
  name: 'App',
  data () {
    return {
      firesData: [], // initial values
      currentFiresData: [], // values filtered by the date picker
      loading: true, // show a spinner if the app is loading data
      title: 'Fire Tracker'.split('') // quick way to iterate over the string to add hover effect
    }
  },
  components: {
    MapComponent, // main map is rendered here
    DatePicker, // using the standard HTML5 date picker, ugly but effective
    ShowMessage // showing how many fires are active with current date range
  },
  // making the API request as soon as the component mounts so data is made avaiable
  async mounted () {
    try {
      // https://jsonkeeper.com/b/8W8M didn't have CORS allowed, so I found a solution by having it served elsewhere
      const data = await axios.get('https://api.npoint.io/2d98a05cabb34e5ea2c6')
      // apparently the API returns an unsorted array
      this.firesData = this.currentFiresData = await data.data.sort((a, b) => new Date(a.date) - new Date(b.date))
      this.loading = false // hiding the spinner when the API is done loading
    } catch (error) {
      console.log('error', error) // just in case
    }
  },
  methods: {
    // triggered by an emit event on DatePicker
    async handleDateChange (newDates) {
      const newStartDate = new Date(newDates.start)
      const newEndDate = new Date(newDates.end)
      this.loading = true // should take a split second but still browser might be very slow in parsing for some reason, so showing spinner
      const filteredDates = await this.firesData.filter(e => {
        return new Date(e.date) >= newStartDate && new Date(e.date) <= newEndDate // filtering selected the date range
      })
      this.currentFiresData = filteredDates // assign the filtered values to the secondary array
      this.loading = false // and hiding the loading animation
    }
  }
}
</script>
