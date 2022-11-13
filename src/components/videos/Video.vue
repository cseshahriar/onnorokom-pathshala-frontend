<template>
    <div class="columns is-desktop is-mobile">
        <div class="column is-6">
            <h2 class="uploader-name title" title="If not have user name then will show user email">Uploader's Name: {{ author_name }}</h2>
            <iframe class="video-iframe" width="400" height="300" :src="'https://www.youtube.com/embed/' + video.youtube_video_id" frameborder="0" 
                    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
            </iframe>
            <br>
            <h2 class="video-title">Title: {{ video.title }}</h2>

            <span class="count">{{ video.view_count }} views</span>
           
            <button 
                class="button is-small count has-text-success-dark"
                style="border-radius:3px; width: 60px"
                @click="createLike"><i class="fas fa-thumbs-up"></i></button>

            <span class="count"> {{ video.like_count }}</span>

            <button
                class="button is-small count has-text-danger-dark"
                style="border-radius:3px; width: 60px"
                @click="createDislike"><i class="fas fa-thumbs-down"></i></button>
            <span class="count"> {{ video.dislike_count }}</span>
        </div>

        <div class="column is-3">
            <h2 class="subtitle">User list of who likes</h2>
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

        <div class="column is-3">
            <h2 class="subtitle">User list of who Dislikes</h2>
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
import {toast} from 'bulma-toast'

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
        created_user: '',
    }
  },
  mounted() {
    this.getVideo()
  },
  methods: {
    // get video
    async getVideo() {
        this.$store.commit('setIsLoading', true)
        const videoID = this.$route.params.id

        let axiosConfig = {
            headers: {
                'Authorization': this.$store.state.token 
            }
        };

        await axios
            .get(`http://127.0.0.1:8000/api/videos/${videoID}/`)
            .then(response => {
                this.video = response.data
                this.created_user = this.video.created_user    
            })
            .catch(error => {
                console.log(error)
            })
            
            await axios
                    .get(
                        `http://127.0.0.1:8000/api/users/${this.created_user}/`, 
                        axiosConfig
                    )
                    .then(response => {
                        console.log('response user data', response.data)
                        
                        let name = ''
                        if(response.data.first_name){
                            name += response.data.first_name
                        } 
                        if(response.data.last_name) {
                            name += ' ' + response.data.last_name
                        }
                        if (!response.data.first_name || !response.data.last_name) {
                            name = response.data.email
                        }
                        
                        this.author_name = name
                        console.log('video user response 133', response.data)
                    })
                    .catch(error => {
                        console.log('video user name 136', error)
                    })
            
            // video liked
            let video_liked_user_ids = {
                'user_ids': this.video.likes.map((item) => item.created_user)
            }
            await axios
                    .post(
                        `http://127.0.0.1:8000/api/video/users`,
                        video_liked_user_ids
                    )
                    .then(response => {
                        console.log('response liked user data', response.data)
                        this.liked_users = response.data
                    })
                    .catch(error => {
                        console.log(error)
                    })
        
            // disliked_users
            let video_disliked_user_ids = {
                'user_ids': this.video.dislikes.map((item) => item.created_user)
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
    // create like
    async createLike() {
        console.log('create like call')
        this.$store.commit('setIsLoading', true)

        const likeData = {
            video: parseInt(this.video.id),
            created_user: parseInt(this.$store.state.user.id)
        }
        
        // create like
        await axios
        .post(
            `http://127.0.0.1:8000/api/likes/`,
            likeData
        )
        .then(response => {
            console.log('liked user data', response.data)
        })
        .catch(error => {
            console.log(error)
        })

        // dislike delete
        await axios
        .post(
            `http://127.0.0.1:8000/api/dislike/delete/`,
            likeData
        )
        .then(response => {
            console.log('del dislike response', response.data)
            this.getVideo()
        })
        .catch(error => {
            console.log(error)
        })

        this.$store.commit('setIsLoading', false)
    },
    // create display
    async createDislike() {
        console.log('create dislike call')
        this.$store.commit('setIsLoading', true)

        const dislikeData = {
            video: parseInt(this.video.id),
            created_user: parseInt(this.$store.state.user.id)
        }

        await axios
        .post(
            `http://127.0.0.1:8000/api/dislikes/`,
            dislikeData
        )
        .then(response => {
            console.log('disliked data', response.data)
        })
        .catch(error => {
            console.log(error)
        })

        // dislike delete
        await axios
        .post(
            `http://127.0.0.1:8000/api/like/delete/`,
            dislikeData
        )
        .then(response => {
            console.log('del like response', response.data)
            this.getVideo()
        })
        .catch(error => {
            console.log(error)
        })

        this.$store.commit('setIsLoading', false)
    }
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
    margin-left: 20px;
    font-weight: 500;
  }
  .video-iframe {
    width: 100%;
    height: 450px;
  }
  .video-title{
    margin-top: 20px;
    margin-bottom: 15px;
  }
  .uploader-name {
    padding-bottom: 20px;
    font-weight: 700;
  }
  table {
    margin-top: 30px;
  }
</style>