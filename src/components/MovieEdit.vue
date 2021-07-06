<template>
    <div>
        <label for="title">Title</label><br/>
        <input id="title" placeholder="title" v-model="localMovie.title"><br>
        <label for="description">Description</label><br/>
        <textarea id="description" placeholder="description" v-model="localMovie.description"></textarea><br>
        <button @click="saveMovie()">Save Movie</button>
    </div>
</template>
<script>
export default {
    name: "MovieEdit",
    props:['movie','token'],
    data(){
        return{
            localMovie:{...this.movie}
        }
    },
    created(){
        if(this.$cookies.isKey('mr-token')){
            this.token = this.$cookies.get('mr-token');
        }
        else{
             this.$router.push('/auth');
        }
    },
    watch:{
        movie: function(newVal, oldVal){
            if(newVal !== oldVal){
                this.localMovie = {...this.movie}
            }
        }
    },
    methods:{
        saveMovie(){
            if(this.movie.id){
                fetch(`http://127.0.0.1:8000/api/movies/${this.movie.id}/`, {
                    method: 'PUT',
                    headers: {
                        'Content-Type' : 'application/json',
                        'Authorization': `Token ${this.token}`
                    },
                    body: JSON.stringify({title: this.localMovie.title, description: this.localMovie.description})
                })
                .then( resp => resp.json())
                .then( () => {
                    this.$emit('update');
                    console.log(this.movie.id);
                })
                .catch( error => console.log(error))
            }
            else{
                fetch(`http://127.0.0.1:8000/api/movies/`, {
                    method: 'POST',
                    headers: {
                        'Content-Type' : 'application/json',
                        'Authorization': `Token ${this.token}`
                    },
                    body: JSON.stringify({title: this.localMovie.title, description: this.localMovie.description})
                })
                .then( resp => resp.json())
                .then( () => {
                    this.$emit('update');
                })
                .catch( error => console.log(error))
            }
        }
    }
}
</script>

<style scoped>

</style>