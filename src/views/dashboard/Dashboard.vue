<template>
    <div>
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">Dashboard</h1>
            </div>

            <div class="column is-6">
                <div class="box has-background-light">
                    <h1>Profile</h1>
                    <h3 v-if="this.$store.state.user.name">Name: {{ this.$store.state.user.name}}</h3>
                    <h3>Email: {{ this.$store.state.user.username}}</h3>
                </div>

                <div class="box has-background-light" style="margin-top:50px; padding-bottom: 65px;">
                    <h2 class="subtitle">Uploaded videos</h2>
                    <table class="table">
                        <thead>
                            <tr>
                                <th>#</th>
                                <th>Title</th>
                                <th>Views</th>
                                <th>Like</th>
                                <th>Dislike</th>
                                <th>Action</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(obj, index) in user_videos" :key="obj.id">
                                <td>{{ index + 1}}</td>
                                <td>
                                    {{ obj.title }}
                                </td>
                                <td>{{ obj.view_count }}</td>
                                <td>{{ obj.like_count }}</td>
                                <td>{{ obj.dislike_count }}</td>
                                <td>
                                    <router-link :to="{ name: 'Video', params: { id: obj.id }}"> <i class="fa fa-eye"></i></router-link>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>

            <div class="column is-6">
                <div class="box has-background-light">
                   <CreateVideo/>
                </div>
            </div>
        </div>
    </div>    
</template>

<script>
    import axios from 'axios'
    import {toast} from 'bulma-toast'
    import CreateVideo from '@/components/videos/CreateVideo.vue';

    export default {
        name: 'Dashboard',
        components: {
            CreateVideo
        },
        data(){
            return {
                user_videos: [],
                user_id: parseInt(this.$store.state.user.id)
            }
        },
        beforeMount() { 
            this.getUserVideo()
        },
        methods: {
            async logout() {
                const formData = {
                    user_id: this.$store.state.user.id,
                }
                await axios
                    .post('http://127.0.0.1:8000/api/logout/', formData)
                    .then(response => {
                        console.log('logout')
                        axios.defaults.headers.common['Authorization'] = ''     
                        localStorage.removeItem('token')
                        this.$store.commit('removeToken')
                        this.$router.push('/log-in')
                    })
                    .catch(
                        error => {
                            console.log(JSON.stringify(error));
                        }
                    )
            },
            async getUserVideo() {
                await axios
                    .get(`http://127.0.0.1:8001/api/user/${this.user_id}/videos/`,)
                    .then(response => {
                        this.user_videos = response.data
                        console.log('user videos', response.data)
                    })
                    .catch(
                        error => {
                            console.log(JSON.stringify(error));
                        }
                    )
            }
        },
    }
</script>

<style scoped>

</style>