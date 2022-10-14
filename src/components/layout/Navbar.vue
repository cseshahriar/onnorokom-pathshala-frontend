<template>
    <nav class="navbar is-dark">
        <div class="navbar-brand">
            <router-link to="/" class="navbar-item">
                <strong>OnnoRokom Pathshala</strong>
            </router-link>
        </div>

        <div class="navbar-menu">
            <div class="navbar-start">
                <template v-if="$store.state.isAuthenticated">
                    <router-link to="/" class="navbar-item">Home</router-link>
                    <router-link to="/dashboard" class="navbar-item">Dashboard</router-link>
                </template>
            </div>

            <div class="navbar-end">
                <template v-if="$store.state.isAuthenticated">
                    <button @click="logout()" class="button is-small is-danger btn-logout">Log out</button>
                </template>

                <div class="navbar-item">
                    <div class="buttons">
                        <template v-if="!$store.state.isAuthenticated">
                            <router-link to="/sign-up" class="button is-success"><strong>Sign Up</strong></router-link>
                            <router-link to="/log-in" class="button is-light"><strong>Log In</strong></router-link>
                        </template>
                    </div>
                </div>
            </div>
        </div>
    </nav>
</template>

<script>
    import axios from 'axios'
    import {toast} from 'bulma-toast'
    export default {
        name: 'Navbar',
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
        }
    }
</script>

<style scoped>
.btn-logout {
    margin-top: 12px;
}
</style>
