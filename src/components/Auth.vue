<template>
    <div class="auth-container">
        <h2 v-if="wrongCred">Wrong credentials entered!. Please enter your correct details.</h2>
        <label for="username">username</label><br/>
        <input id="username" placeholder="username" v-model="username"><br>
        <label for="password">password</label><br/>
        <input id="password" placeholder="password" v-model="password" type="password"><br>
        <button @click="login()" v-if="loginMode">Login</button>
        <button @click="register()" v-else>Register</button>
        <p @click="loginMode = false" v-if="loginMode">Don't have an account yet? Register Here</p>
        <p @click="loginMode = true" v-else>you already have an account. Login here</p>
    </div>
</template>

<script>
export default {
    name: "Auth",
    data(){
        return{
            username: '',
            password: '',
            loginMode: true,
            wrongCred: false
        }
    },
    created(){
        if(this.$cookies.isKey('mr-token')){
            this.$router.push('/');
        }
    },
    methods:{
        errorLogin(){
            this.$cookies.remove('mr-token');
            this.$router.push('/auth');
        },
        login(){
            fetch(`http://127.0.0.1:8000/auth/`, {
                    method: 'POST',
                    headers: {
                        'Content-Type' : 'application/json'
                    },
                    body: JSON.stringify({username: this.username, password: this.password})
                })
                .then( resp => {
                            if(resp.ok){
                                return resp.json()
                            }
                            else{
                                this.wrongCred = true;
                                this.errorLogin;     
                            }
                        }
                    )
                .then( res => {
                    if(res){
                        this.$cookies.set("mr-token", res.token, "30d");
                        this.$router.push('/');
                        console.log(res);
                    }
                    else{
                        console.log(res);
                    }
                })
                .catch( error => {
                        console.log(error);
                })
        },
        register(){
            fetch(`http://127.0.0.1:8000/api/users/`, {
                    method: 'POST',
                    headers: {
                        'Content-Type' : 'application/json'
                    },
                    body: JSON.stringify({username: this.username, password: this.password})
                })
                .then( () => {
                    this.login()
                })
                .catch( error => {
                    this.errorLogin();
                    console.log(error);
                })
        }
    }
}
</script>
<style scoped>
    .auth-container{
        width: 50%;
        margin : 50px 25%;
    }

    p {
        cursor : pointer;
    }

    h2 {
        color  : white;
    }
</style>