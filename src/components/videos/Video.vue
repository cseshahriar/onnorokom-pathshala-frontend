<template>
    <div class="columns is-desktop is-mobile">
        <div class="column">
            <iframe class="video-iframe" width="400" height="300" :src="'https://www.youtube.com/embed/' + video.youtube_video_id" frameborder="0" 
                    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
            </iframe>
            <br>
            <h2 class="mb-1">{{ video.title }}</h2>

            <span class="count">{{ video.view_count }} views</span>
           
            <button class="button is-light is-small count">Like</button>
            <span class="count"> {{ video.like_count }}</span>

            <button class="button is-light is-small count">Dislike</button>
            <span class="count"> {{ video.like_count }}</span>
            <h2>Uploader's Name: {{ author_name }}</h2>
        </div>

        <div class="column">
            <h2>User list of who likes</h2>
            <table class="table">
                <thead>
                    <tr>
                        <th>#</th>
                        <th>Name</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>1</td>
                        <td>Shahriar</td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="column">
            <h2>User list of who Dislikes</h2>
            <table class="table">
                <thead>
                    <tr>
                        <th>#</th>
                        <th>Name</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>1</td>
                        <td>Shahriar</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
    
    

</template>

<script>
// @ is an alias to /sc
import axios from 'axios';

export default {
  name: 'Video',
  components: {
  },
  data () {
    return {
        video: {},
        author_name: ''
    }
  },
  mounted() {
    this.getVideo()
  },
  methods: {
    async getVideo() {
        this.$store.commit('setIsLoading', true)
        const videoID = this.$route.params.id

        await axios
            .get(`http://127.0.0.1:8001/api/videos/${videoID}/`)
            .then(response => {
                this.video = response.data
                let author_id = response.data.author_id
                console.log('response data', author_id, response.data)
                
                let axiosConfig = {
                    headers: {
                    'Authorization': this.$store.state.token 
                    }
                };
                axios
                    .get(`http://127.0.0.1:8000/api/users/${author_id}/`, axiosConfig)
                    .then(response => {
                        console.log('response user data', response.data)
                        
                        let name = ''
                        if(response.data.first_name){
                            name += response.data.first_name
                        } 
                        if(response.data.last_name) {
                            name += response.data.last_name
                        }
                        if (!response.data.first_name || !response.data.last_name) {
                            name = response.data.email
                        }
                        
                        this.author_name = name
                    })
                    .catch(error => {
                        console.log(error)
                    })
                
            })
            .catch(error => {
                console.log(error)
            })
        
        this.$store.commit('setIsLoading', false)
    },
  }, 
  created() {
  }
}
</script>


<style scoped>
  .title {
    margin-bottom: 15px m !important;
  }
  .subtitle {
    margin-bottom: 30px !important;
    margin-top: 15px !important;
  }
  .count {
    display: inline-block;
    margin-right: 10px;
    font-weight: 500;
  }
  .video-iframe {

  }
</style>