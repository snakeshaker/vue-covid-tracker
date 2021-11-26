<template>
  <main v-if="!loading">
    <data-title :text="title" :dataDate="dataDate" />
    <data-boxes :stats="stats" />
    <country-select @get-country="getCountryData" :countries="countries" />
    <button
      v-if="stats.Country"
      class="bg-green-700 text-white rounded p-3 mt-10 focus:outline-none hover:bg-green-600"
      @click="clearCountryData"
    >
      Clear Country
    </button>
  </main>
  <main v-else class="flex flex-col align-center justify-center text-center">
    <div class="text-gray-5 text-3xl mt-10 mb-6">
      Loading, please wait...
    </div>
    <img :src="loadingImage" class="w-24 m-auto" alt="Loading...">
  </main>
</template>

<script>
import DataTitle from '@/components/DataTitle.vue'
import DataBoxes from '@/components/DataBoxes.vue'
import CountrySelect from '@/components/CountrySelect.vue'

export default {
  name: 'Home',
  components: {
    DataTitle,
    DataBoxes,
    CountrySelect
  },
  data() {   
    return {
      loading: true,
      title: 'Global',
      dataDate: '',
      stats: {},
      countries: [],
      loadingImage: require('../assets/hourglass.gif'),
    }
  },
  methods: {
    async fetchCovidData() {
      const res = await fetch('https://api.covid19api.com/summary');
      const data = await res.json();
      return data;
    },
    getCountryData(country) {
      this.stats = country;
      this.title = country.Country;
    },
    async clearCountryData() {
      this.loading = true;
      const data = await this.fetchCovidData();
      this.title = 'Global';
      this.stats = data.Global;
      this.loading = false;
    },
  },
  async created() {
    const data  = await this.fetchCovidData();
    this.dataDate = data.Date;
    this.stats = data.Global;
    this.countries = data.Countries;
    this.loading = false;
  }
}
</script>
