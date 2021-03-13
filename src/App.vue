<template>
  <div id="app">
    <div v-if="!loading" >
      <ShowMessage :message="message"
        :title="title"/>
      <DatePicker
        v-bind:defaultDates="shownDates"
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
      shownDates: '', // datepicker shows these after loading the app for the first time
      loading: true, // show a spinner if the app is loading data
      title: 'Fire Tracker'.split(''), // quick way to iterate over the string to add hover effect
      message: '' // shows number of fires or errors
    }
  },
  components: {
    MapComponent, // main map is rendered here
    DatePicker, // using the standard HTML5 date picker, ugly but effective
    ShowMessage // showing how many fires are active with current date range
  },
  async mounted () { // making the API request as soon as the component mounts so data is made avaiable
    try {
      // https://jsonkeeper.com/b/8W8M didn't have CORS allowed, so I found a solution by having it served elsewhere
      const data = await axios.get('https://api.npoint.io/2d98a05cabb34e5ea2c6')
      // apparently the API returns an unsorted array
      this.firesData = this.currentFiresData = await data.data.sort((a, b) => new Date(a.date) - new Date(b.date))
      this.shownDates = { start: this.currentFiresData[0].date, end: this.currentFiresData[this.currentFiresData.length - 1].date }
      this.message = `Currently showing ${this.currentFiresData.length} fires.`
      this.loading = false // hiding the spinner when the API is done loading
    } catch (error) {
      this.message = 'Sorry, there was an error while processing the request.' // just in case
      console.log('error', error)
    }
  },
  methods: { // triggered by an emit event on DatePicker
    async handleDateChange (newDates) { // probably overkill to do this asynchronously, but browser might me slow for some reason
      const newStartDate = new Date(newDates.start) // need date objects to parse results
      const newEndDate = new Date(newDates.end)
      if (newEndDate >= newStartDate) {
        this.loading = true // if all goes well should show only for a split second
        const filteredDates = await this.firesData.filter(e => {
          return new Date(e.date) >= newStartDate && new Date(e.date) <= newEndDate // filtering selected the date range
        })
        this.currentFiresData = filteredDates // assign the filtered values to the secondary array
        this.message = `Currently showing ${this.currentFiresData.length} fires.`
        this.loading = false // and hiding the loading animation
      } else { // if the user selects an end date that is before the start date (sigh)
        this.message = 'Start date must be before end date.'
        this.showDates = { start: this.firesData[0].date, end: this.firesData[this.firesData.length - 1].date }
      }
    }
  }
}
</script>
