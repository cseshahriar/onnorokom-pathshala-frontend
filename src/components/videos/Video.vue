<template>
    <div class="columns is-desktop is-mobile">
        <div class="column">
            <iframe class="video-iframe" width="400" height="300" :src="'https://www.youtube.com/embed/' + video.youtube_video_id" frameborder="0" 
                    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
            </iframe>
            <br>
            <h2 class="video-title">{{ video.title }}</h2>

            <span class="count">{{ video.view_count }} views</span>
           
            <button class="button is-light is-small count">Like</button>
            <span class="count"> {{ video.like_count }}</span>

            <button class="button is-light is-small count">Dislike</button>
            <span class="count"> {{ video.like_count }}</span>
            <h2 class="uploader-name" title="If not have user name then will show user email">Uploader's Name: {{ author_name }}</h2>
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
                    <tr v-for="(like_user, index) in liked_users" :key="like_user.id">
                        <td>{{ index + 1}}</td>
                        <td>{{ like_user.email }}</td>
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
                    <tr v-for="(dislike_user, index) in disliked_users" :key="dislike_user.id">
                        <td>{{ index + 1}}</td>
                        <td>{{ dislike_user.email }}</td>
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
        author_name: '',
        liked_users: [],
        disliked_users: [],
    }
  },
  mounted() {
    this.getVideo()
  },
  methods: {
    async getVideo() {
        this.$store.commit('setIsLoading', true)
        const videoID = this.$route.params.id

        let axiosConfig = {
            headers: {
                'Authorization': this.$store.state.token 
            }
        };

        await axios
            .get(`http://127.0.0.1:8001/api/videos/${videoID}/`)
            .then(response => {
                this.video = response.data
                console.log('response data', response.data)        
            })
            .catch(error => {
                console.log(error)
            })
            
            let author_id = this.video.author_id
            await axios
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
            
            // video liked
            let video_liked_user_ids = {
                'user_ids': this.video.likes.map((item) => item.user_id)
            }
            console.log('video_liked_user_ids', video_liked_user_ids)
            await axios
                    .post(
                        `http://127.0.0.1:8000/api/video/users`,
                        video_liked_user_ids
                    )
                    .then(response => {
                        console.log('liked user data', response.data)
                        this.liked_users = response.data
                    })
                    .catch(error => {
                        console.log(error)
                    })
        
            // disliked_users
            let video_disliked_user_ids = {
                'user_ids': this.video.dislikes.map((item) => item.user_id)
            }
            console.log('video_disliked_user_ids', video_disliked_user_ids)
            await axios
                    .post(
                        `http://127.0.0.1:8000/api/video/users`,
                        video_disliked_user_ids
                    )
                    .then(response => {
                        console.log('disliked user data', response.data)
                        this.disliked_users = response.data
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
  .video-title{
    margin-bottom: 10px;
  }
  .uploader-name {
    padding-top: 20px;
    font-weight: 700;
  }
</style>