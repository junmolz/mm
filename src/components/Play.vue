<template>
  <div class="play">
    <div class="to-search" @click="toSearch">
      <span class="glyphicon glyphicon-search"></span>
    </div>
    <div class="container" style="padding-bottom:0px">
      <div class="row" >
        <div class="page-header text-left">
          <h1><span>&nbsp&nbsp</span>  {{song.name}} <small>{{song.songer}}</small></h1>
        </div>
      </div><!-- /.row -->
    </div>
    
      <audio id="music" :src="'http://ws.stream.qqmusic.qq.com//'+song.id+'.m4a?fromtag=46'" 
      v-on:timeupdate="updateTime" v-bind:autoplay="true"
        @ended="next"
      
      ></audio>
      
      <div id="lyric-box" class="container lyric">
        <div class="row" >
          <div id="lyric" class="lyric-p col-lg-4 col-md-6 col-sm-8 col-xs-12 col-lg-offset-4 col-md-offset-3 col-sm-offset-2" :style="{'top': 150-30*song.lyTop+'px'}">
            <p v-for="data in lyrics" :class="{'active':data[0]==song.num}">{{data[1]}}</p>
            
          </div>
        </div>
      </div>
      
      <div id="audio-nav" class="audio-nav affix ">
        <div class="container">
        
          <div class="row" style="position:relative;top:15px;">
            <div class="progress  audio-nav center-block" style="height:6px; position:relative; min-width:500px;"
            @click="changeProgress">
              <div class="progress-bar progress-bar-warning" role="progressbar" aria-valuenow="20" aria-valuemin="0" aria-valuemax="100" :style="{width:progress + '%'}"
              
              >
              </div>
              
            </div>
            <span class="paly-time" style="left:0;">{{curt}}</span>
            <span class="paly-time" style="right:0px;">{{durt}}</span>
          </div>
          
          <div class="row my-play">
            <div class="col-lg-1 col-md-1 col-xs-1 col-sm-1">
              <span class="glyphicon glyphicon-refresh"></span>
            </div>
            <div class="col-lg-4 col-md-4 col-xs-4 col-sm-4 col-lg-offset-3  col-md-offset-3 col-xs-offset-3 col-sm-offset-3">
              <span class="glyphicon glyphicon-step-backward" @click="prev"></span>
              <span id="pause" class="glyphicon glyphicon-pause" @click="Tpause"></span>
              <span class="glyphicon  glyphicon-step-forward" @click="next"></span>
            </div>
            <div class="col-lg-1 col-md-1 col-xs-1 col-sm-1 col-lg-offset-3 col-md-offset-3 col-xs-offset-3 col-sm-offset-3">
              <div :class="{'btn-group':1, 'dropup pull-right':1, 'open':show}"
              @click="show = !show;">
                <button type="button" class="btn btn-default dropdown-toggle">
                  <span class="glyphicon glyphicon-menu-hamburger"></span>
                </button>
                <ul class="dropdown-menu">
                  <li @click="playIt(index)" v-for="(val,index) in list"><a href="#">{{val.name}} <small>{{val.singer}}</small></a></li>
                  
                </ul>
              </div>
            </div>
          </div>
          
        
        </div>
      </div>
      
      
    </div>

</template>

<script>

import Base64 from '../base64.js'
export default {
  name: 'Play',
  props:['songInfo'],
  data () {
    return {
      song:this.songInfo,
      list:[{name:'平凡之路',songer:'朴树',lyArr:[],num:0,lyTop:0,id:7072290},{name:'凉凉',songer:'杨宗纬/张碧晨',lyArr:[],num:0,lyTop:0,id:200380820},{name:'一生所爱',songer:'莫文蔚',lyArr:[],num:0,lyTop:0,id:204292910}],
      lyrics: null,
      now:0,
      show:false,
      currentT: 0,
      dur: 0
    }
  },
  mounted () {
    var lyricBox = document.querySelector('#lyric-box');
    var ly = document.querySelector('#lyric');
    var bot = document.querySelector('#audio-nav');
    ;
    lyricBox.style.height = bot.getBoundingClientRect().top - lyricBox.getBoundingClientRect().top - 45 + 'px';
    this.$http.jsonp('https://api.darlin.me/music/lyric/' + this.songInfo.id, {
          jsonp: 'callback'
        }).then(function (response) {
          this.lyrics = Base64
            .decode(response.data.lyric)
            .split('[')
            .slice(5)
            .map(str => {
              let t = str.split(']')
              return {[t[0]]: [[t[0]], t[1]]}
            })
            .reduce((a, b) => {
              return {...a, ...b}
              
            }) 
        })
        
  },
  methods:{
    toSearch(){
      this.$parent.showW = '';
    },
    updateTime(){
      this.currentT = parseInt(document.querySelector('#music').currentTime);
      this.dur = parseInt(document.querySelector('#music').duration);
    },
    Tpause(){
      var music = document.querySelector('#music');
      if(music.paused){
        document.querySelector('#pause').className = "glyphicon glyphicon-pause";
        music.play();
        
      }else{
        music.pause();
        document.querySelector('#pause').className = "glyphicon glyphicon-play";
      }
    },
    prev(){
      this.now  = this.now<=0? this.list.length-1 :--this.now;
      this.playIt(this.now);
    },
    next(){
      this.now  = this.now>=this.list.length-1? 0 :++this.now;
      this.playIt(this.now);
    },
    playIt(index){
      this.song = this.list[index];
      this.lyrics = '';
      this.getLyric(this.song);
      this.now = index;
    },
    getLyric(ele){
      this.$http.jsonp('https://api.darlin.me/music/lyric/' + ele.id, {
            jsonp: 'callback'
          }).then(function (response) {
            this.lyrics = Base64
              .decode(response.data.lyric)
              .split('[')
              .slice(5)
              .map(str => {
                let t = str.split(']')
                return {[t[0]]: [[t[0]], t[1]]}
              })
              .reduce((a, b) => {
                return {...a, ...b}
                
              }) 
          })
    },
    changeProgress(e){
      var target;
      if (e.target.classList.contains('progress')) {
        target = e.target;
      }else{
        target = e.target.parentNode;
      }
        var x = parseInt(getComputedStyle(target).width);
        this.currentT = e.offsetX/x * this.dur;
        document.querySelector('#music').currentTime = this.currentT;
        console.log(this.lyrics);
    }
  },
  computed:{
    curt: function(){
      var seconds = this.currentT%60;
      if (seconds<10) {
        seconds = '0' + seconds;
      }
      return parseInt(this.currentT/60) + ':' + seconds;
    },
    durt: function(){
      var seconds = this.dur%60;
      if (seconds<10) {
        seconds = '0' + seconds;
      }
      return parseInt(this.dur/60) + ':' + seconds;
    },
    progress(){
      var lyTime,lyr = 0;
      console.log(this.song.lyArr.length);
      if (!this.song.lyArr.length) {
        for (var item in this.lyrics) {
          lyTime = item.split(':');
          lyTime = parseInt(lyTime[0]) * 60 + parseInt(lyTime[1]);
          if (this.lyrics[item][1].length>2) {
            this.song.lyArr.push([lyTime,item,lyr++]);
          }else{
            this.song.lyArr.push([lyTime,item,lyr]);
          }
          
        }
      }
      for (var i = 0; i < this.song.lyArr.length; i++) {
        if (this.currentT === this.song.lyArr[i][0]) {
          this.song.num = this.song.lyArr[i][1];
            this.song.lyTop = this.song.lyArr[i][2];
            
        }
      }
      return this.currentT/this.dur *100;
    }
  
  },
  watch:{
    songInfo:function(){
      this.song = this.songInfo;
      this.lyrics = '';
      this.list.push(this.songInfo);
      this.getLyric(this.songInfo);
    }
  }
}
</script>
<style scoped>
html { overflow: hidden;}
.to-search{
  position: fixed;
  top: 55px;
  right: 35px;
  font-size: 40px;
  color: #fff;
}
.lyric{
  position: relative;
  overflow: hidden;
  color: rgb(129, 129, 129);
}
.lyric p{
  font: 14px/20px arial;
  letter-spacing: 2px;
  
}
.lyric .active{
  opacity: 1;
  color:#dae83a;
}
#lyric{
  position: absolute;
  transition: 300ms;
}
.audio-nav{
  bottom: 40px;
  height: 40px;
  width: 100%;
  min-width:500px;
}
.paly-time{
  color: #fff;
  position:absolute;
  top:-20px;
}
.my-play{
  color: #fff;
  font-size: 30px;
}
</style>