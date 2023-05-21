<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-6">
        <div class="q-mb-md">
          <q-input outlined v-model="apiKey" label="YouTube API Key" @keyup.enter="saveKey" />
          <q-btn label="Save Key" @click="saveKey" />
        </div>
        <div class="q-mb-md">
          <q-input outlined v-model="searchQuery" label="Search Query" @keyup.enter="search" />
          <q-btn label="Search" @click="search" />
        </div>
        <q-btn label="Analyze" @click="analyze" />
        <q-table
  :rows="videos"
  :columns="columns"
  row-key="videoId"
  :loading="loading"
  :rows-class="row => ({ 'bg-green': row.views > row.subscribers })"
  virtual-scroll-horizontal
>
  <template v-slot:body-cell-thumbnail="props">
    <q-td :props="props">
      <a :href="`https://www.youtube.com/watch?v=${props.row.videoId}`" target="_blank">
        <img :src="props.row.thumbnail" alt="Thumbnail" class="q-ma-md" style="width: 50px; height: 50px;"/>
      </a>
    </q-td>
  </template>
</q-table>


      
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      apiKey: '',
      searchQuery: '',
      videos: [],
      loading: false,
      columns: [
      { name: 'thumbnail', required: true, label: 'Thumbnail', align: 'left', field: 'thumbnail', format: (val) => `<img src="${val}" alt="Thumbnail"/>` },

        { name: 'title', required: true, label: 'Title', align: 'left', field: 'title' },
        
        { name: 'publishedAt', required: true, label: 'Publication Date', align: 'left', field: 'publishedAt' },
        { name: 'channelTitle', required: true, label: 'Channel Title', align: 'left', field: 'channelTitle' },
        { name: 'views', required: true, label: 'Views', align: 'left', field: 'views' },
        { name: 'likes', required: true, label: 'Likes', align: 'left', field: 'likes' },
        { name: 'duration', required: true, label: 'Video Length', align: 'left', field: 'duration' },
        { name: 'subscribers', required: true, label: 'Subscribers', align: 'left', field: 'subscribers' },
        
      ]
    }
  },
  methods: {
    async saveKey() {
      this.loading = true
      try {
        await axios.post('http://localhost:5000/api/save_key', { key: this.apiKey })
      } catch (err) {
        this.$q.notify({
          type: 'negative',
          message: `Error: ${err.message}`
        })
      }
      this.loading = false
    },
    async search() {
      this.loading = true
      try {
        const response = await axios.get('http://localhost:5000/api/search', { params: { q: this.searchQuery } })
        this.videos = response.data      } catch (err) {
      this.$q.notify({
        type: 'negative',
        message: `Error: ${err.message}`
      })
    }
    this.loading = false
  },
  async analyze() {
    this.loading = true
    try {
      const response = await axios.get('http://localhost:5000/api/analyze')
      this.videos = response.data
    } catch (err) {
      this.$q.notify({
        type: 'negative',
        message: `Error: ${err.message}`
      })
    }
    this.loading = false
  },
}
}
</script>

     
