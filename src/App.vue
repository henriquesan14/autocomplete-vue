<template>
  <div id="app">
    <Autocomplete :shouldReset="false" 
    title="Selecione..."
     :items="users" filterby="name" @selected="userSelected" @change="onChange"/>
  </div>
</template>

<script>
import Autocomplete from './components/Autocomplete.vue'
import axios from 'axios';
export default {
  name: 'app',
  components: {
    Autocomplete
  },
  async mounted(){
    try{
      const res = await axios.get('https://jsonplaceholder.typicode.com/users');
      this.users = res.data;
    }catch(err){
      console.log(err);
    }
  },
  data(){
    return {
      users: []
    }
  },
  methods: {
    userSelected(user){
      // console.log(user);
    },
    onChange(value){
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0px auto;
  width: 400px;
  margin-top:60px;
}
</style>
