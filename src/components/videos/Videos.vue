<template>
    <div class="columns">
        <div class="column is-full">
            <h4 class="mb-3 title text-center">Welcome to OnnoRokom Pathshala</h4>
            <p class="subtitle">List of latest uploaded videos</p>
        </div>
    </div>

      <div class="columns is-multiline is-mobile">
        <!-- single video -->
        <div class="column is-one-quarter mb-5" v-for="video in videos" v-bind:key="video.id">
            <iframe width="350" height="200" :src="'https://www.youtube.com/embed/' + video.youtube_video_id" frameborder="0" 
              allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
            </iframe>
          <br>
          <h2 class="video-title">{{ video.title }}</h2>
         
          <span class="count">{{ video.view_count }} views</span>
          <span class="count">{{ video.like_count }} Likes</span>
          <span class="count">{{ video.like_count }} Dislike</span>

          <router-link :to="{ name: 'Video', params: { id: video.id }}"><i class="fa fa-eye"></i></router-link>
        </div>
        <!-- / single video -->
      </div>
</template>

<script>
// @ is an alias to /sc
import axios from 'axios';
// @ is an alias to /src

export default {
  name: 'Videos',
  components: {
  
  },
  props: {
      videos: [],
  },
  data () {
    return {
        
    }
  },
  methods: {
  },
  created () {
    // work a page init
  },
}
</script>


<style scoped>
  .nicefont {
    font-size: 18px;
    font-family: 'Alatsi', sans-serif;
  }
  .title {
    margin-bottom: 15px m !important;
  }
  .subtitle {
    margin-bottom: 30px !important;
    margin-top: 15px !important;
  }
  .count {
    display: inline-block;
    margin-right: 20px;
    font-weight: 700;
  }
  .video-title{
    margin-bottom: 10px;
  }
</style>