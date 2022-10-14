<template>
    <div class="container">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Log In</h1>
                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label>Email</label>
                        <div class="control">
                            <input type="email" name="username" v-model="username" class="input">
                        </div>
                    </div>

                    <div class="field">
                        <label>Password</label>
                        <div class="control">
                            <input type="password" name="password" v-model="password" class="input">
                        </div>
                    </div>

                    <div class="notification is-warning" v-if="errors.length">
                        <p v-for="error in errors" :key="error">{{ error }}</p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-success">Submit</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    </div>    
</template>

<script>
    import axios from 'axios'
    import {toast} from 'bulma-toast'

    export default {
        name: 'LogIn',
        data() {
            return {
                username: '',
                password: '',
                errors: []
            }
        },
        methods: {
            async submitForm() {
                this.$store.commit('setIsLoading', true)

                axios.defaults.headers.common['Authorization'] = '' 
                localStorage.removeItem('token')

                this.errors = []

                // validation
                if(this.username === '') {
                    this.errors.push('The Email is missing')
                }

                if(this.password === '') {
                    this.errors.push('The password is missing')
                }

                if(!this.errors.length) {
                    const formData = {
                        username: this.username,
                        password: this.password,
                    }

                    await axios
                    .post('/api/auth/', formData)
                    .then(response => {
                        const token = response.data.auth_token
                        this.$store.commit('setToken', token)
                        axios.defaults.headers.common['Authorization'] = 'Token ' + token
                        localStorage.setItem('token', token)

                        console.log('log user ', response.data)

                        this.$store.commit(
                            'setUser', {
                                'id': response.data.user_id,
                                'username': response.data.email,
                                'name': response.data.name,
                            }
                        )
                        localStorage.setItem('userid', response.data.user_id)
                        localStorage.setItem('username', response.data.email)
                        localStorage.setItem('name', response.data.name)

                        // redirect home
                        this.$router.push('/dashboard')
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
                }
            }

        }
    }
</script>


<style scoped>

</style>