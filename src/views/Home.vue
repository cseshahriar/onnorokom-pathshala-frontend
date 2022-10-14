<template>
  <div class="home">
    <Videos v-bind:videos="videos"></Videos>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import Videos from '@/components/videos/Videos.vue'

export default {
  name: 'Home',
  components: {
    Videos
  },
  data () {
    return {
      videos: [],
    }
  },
  methods: {
    getVideos() {
        axios.get('http://127.0.0.1:8001/api/videos/')
        .then(response => (this.videos = response.data) )
        .catch(err => console.log(err))
        console.log(this.videos) 
    },
  },
  created () {
    // work a page init
    this.getVideos();
    createNew:false;
  },
}
</script>
