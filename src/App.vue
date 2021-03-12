<template>
  <div id="app">
    <div v-if="!loading" >
      <DatePicker v-bind:defaultDates="{ start: firesData[0].date, end: firesData[firesData.length - 1].date }"
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
import '@/assets/styles/App.scss'

export default {
  name: 'App',
  data () {
    return {
      firesData: [],
      currentFiresData: [],
      loading: true
    }
  },
  components: {
    MapComponent,
    DatePicker
  },
  // making the API request as soon as the component mounts so data is made avaiable
  async mounted () {
    try {
      // https://jsonkeeper.com/b/8W8M didn't have CORS allowed, so I found a solution by having it served elsewhere
      const data = await axios.get('https://api.npoint.io/2d98a05cabb34e5ea2c6')
      this.firesData = await data.data.sort((a, b) => new Date(a.date) - new Date(b.date))
      this.currentFiresData = this.firesData
      this.loading = false
    } catch (error) {
      console.log('error', error)
    }
  },
  methods: {
    async handleDateChange (newDates) {
      const newStartDate = newDates.startDate
      const newEndDate = newDates.endDate
      this.loading = true
      const filteredDates = await this.firesData.filter(e => {
        return e.date >= newStartDate && e.date <= newEndDate
      })
      this.currentFiresData = filteredDates
      this.loading = false
    }
  }
}
</script>
