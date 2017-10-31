<template>
  <div id="app">
    <Play :songInfo="songInfo" v-show="showW"></Play>
    <Search v-show="!showW"></Search>
  


  </div>  
</template>
 
<script>
import Search from './components/Search'
import Play from './components/Play'
export default {
  name: 'app',
  data(){
    return {
      showW: '1',
      songInfo:{name:'平凡之路',songer:'朴树',lyArr:[],num:0,lyTop:0,id:7072290},
    }
  },
  components: {
    Search,
    Play
  }
  
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
html { width: 100%; height:100%; }
html {
  font-family: sans-serif;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%
}

body {
  margin: 0
}
body { 
	width: 100%;
	height:100%;
	font-family: 'Open Sans', sans-serif;
	background-image: url(./assets/img/1.jpg);
	background-size: cover;
}

</style>
