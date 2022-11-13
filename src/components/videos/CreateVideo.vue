<template>
    <div class="card">
        <div class="card-content">
            <h4 class="title is-4">Create Video</h4>
            <form id="formvideo" @submit.prevent="submitForm">
                
                <div class="notification is-warning" v-if="errors.length">
                    <p v-for="error in errors" :key="error">{{ error }}</p>
                </div>
                
                <div class="field">
                    <label for="title" class="label">Title</label>
                    <div class="control">
                        <input type="text" class="input" v-model="title" name="title" id="title" placeholder="Title">
                    </div>
                </div>

                <div class="field">
                    <label for="description" class="label">Description</label>
                    <div class="control">
                        <textarea class="textarea" name="description" id="description" v-model="description" rows="2"></textarea>
                    </div>
                </div>

                <div class="field">
                    <label for="youtube_video_id" class="label">Youtube Video ID</label>
                    <div class="control">
                        <input type="text" class="input" v-model="youtube_video_id" name="youtube_video_id" id="youtube_video_id" placeholder="Youtube Video ID"/>
                    </div>
                </div>
    
        
                <button type="submit" class="button is-success"
                @click="$emit('createVideo')"
                >Create Video</button>
            </form>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import {toast} from 'bulma-toast'

export default {
    name:'CreateVideo',
    components: {
    },
    data() {
        return {
            title: '',
            description: '',
            youtube_video_id: '',
            created_user_id: this.$store.state.user.id,
            errors: []
        }
    },
    mounted() {
        console.log('created_user_id', this.created_user_id)
    },
    methods: {
            async submitForm() {
                this.errors = []
                
                // validation
                if(this.title === '') {
                    this.errors.push('The title is missing')
                }
                if(this.description === '') {
                    this.errors.push('The description is missing')
                }
                if(this.youtube_video_id === '') {
                    this.errors.push('The youtube_video_id is missing')
                }

                if(!this.errors.length) {
                    this.$store.commit('setIsLoading', true)

                    const formData = {
                        title: this.title,
                        description: this.description,
                        youtube_video_id: this.youtube_video_id,
                        created_user: this.created_user_id
                    }

                    await axios
                    .post('http://127.0.0.1:8000/api/videos/', formData)
                    .then(response => {
                        toast({
                            message: 'Video has been created',
                            type: 'is-success',
                            dismissible: true,
                            pauseOnHover: true,
                            duration: 2000,
                            position: 'top-right'
                        })
                        this.$router.push('/log-in')
                    })
                    .catch(error => {
                        if(error.response) {
                            for (const property in error.response.data) {
                                this.errors.push(`${property}: ${error.response.data[property]}`)
                            }
                        } else if(error.message ) {
                            this.errors.push('Something went wrong. Please try again!')
                        }
                    })

                    this.$store.commit('setIsLoading', false)
                    this.$router.push('/')
                }
            }
        }
}
</script>