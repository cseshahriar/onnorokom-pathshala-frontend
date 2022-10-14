<template>
    <div class="container">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">Dashboard</h1>
            </div>

            <div class="column is-6">
                <div class="box has-background-warning">
                    <h3>Name: {{ this.$store.state.user.name}}</h3>
                    <h3>Email: {{ this.$store.state.user.username}}</h3>
                </div>
            </div>
        </div>
    </div>    
</template>

<script>
    import axios from 'axios'
    import {toast} from 'bulma-toast'

    export default {
        name: 'Dashboard',
        data(){
            return {
                
            }
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
            }
        },
        mounted() {
        
        }
    }
</script>

<style scoped>

</style>