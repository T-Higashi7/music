<template>

<p>検索したい曲名を入力してください。</p>
<div>
<input type="text" v-model="term" class="bg-gray-900 text-gray-100">
<button @click="search">検索</button>
</div>


</template>

<script>
export default {

  data () {
    return {
      term:"Thriller"
    }
  },


  methods:{
    
    search(){
      console.log(this.term)

      const term = "We Will Rock You"
      const url = "/search"
      const queryParameter = `?term=${this.term}&media=music&entity=musicTrack`
      
      axios.get(url+queryParameter)
          .then((response) => {
            console.log(response)
      }) 
      
    }
 }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
