<template>
  <div class="Search">
    <div class="container" style="padding-bottom:20px">
      <div class="row" >
          <div class="input-group" col-lg-10>
            <span class="input-group-btn">
              <button class="btn btn-default" type="button"
              @click = toPlay
              ><span class="glyphicon glyphicon-arrow-left"></span></button>
            </span>
            <input type="text" class="form-control" placeholder="要搜索的歌名"
            v-model =  "searchInfo">
            <span class="input-group-btn">
              <button class="btn btn-default" type="button"
              @click = getData(searchInfo)
              ><span class="glyphicon glyphicon-search"></span></button>
            </span>
            
          </div><!-- /input-group -->
      </div><!-- /.row -->
      
    </div>
    
    <div class="container ">
      <div class="row">
        <div class="panel panel-success text-left my-list" v-if="searchRes.song">
          <div class="panel-heading">
            <h3 class="panel-title">{{searchRes.song.name}}</h3>
          </div>
          <div class="panel-body" v-for="(data,index) in searchRes.song.itemlist"
          @click="play(data)">
            <small>{{index+1+'.'}}</small>
            {{data.name}}
            <small>{{data.singer}}</small>
          </div>  </div>
      </div>
      <div class="row">
        <div class="panel panel-success text-left my-list" v-if="searchRes.mv">
          <div class="panel-heading">
            <h3 class="panel-title">{{searchRes.mv.name}}</h3>
          </div>
          <div class="panel-body" v-for="(data,index) in searchRes.mv.itemlist"
          @click="play(data)">
            <small>{{index+1+'.'}}</small>
            {{data.name}}
            <small>{{data.singer}}</small>
          </div>  </div>
      </div>
      <div class="row">
        <div class="panel panel-success text-left my-list" v-if="searchRes.album">
          <div class="panel-heading">
            <h3 class="panel-title">{{searchRes.album.name}}</h3>
          </div>
          <div class="panel-body" v-for="(data,index) in searchRes.album.itemlist"
          @click="play(data)">
            <small>{{index+1+'.'}}</small>
            {{data.name}}
            <small>{{data.singer}}</small>
          </div>  </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Search',
  data () {
    return {
      searchInfo: '',
      searchRes: ''
    }
  },
  mounted (){
    this.$http.jsonp('http://c.y.qq.com/splcloud/fcgi-bin/smartbox_new.fcg', {
      params: {
        is_xml: 0,
        format: 'jsonp',
        key: '动物世界',
        g_tk: 5381,
        loginUin: 0,
        hostUin: 0,
        inCharset: 'utf8',
        outCharset: 'utf-8',
        notice: 0,
        platform: 'yqq',
        needNewCode: 0
      },
      jsonp: 'jsonpCallback'
    }).then((response) => {
      this.searchRes = response.data.data
      return response.data.data;
    })
  },
  methods:{
    getData () {
        this.$http.jsonp('http://c.y.qq.com/splcloud/fcgi-bin/smartbox_new.fcg', {
          params: {
            is_xml: 0,
            format: 'jsonp',
            key: this.searchInfo,
            g_tk: 5381,
            loginUin: 0,
            hostUin: 0,
            inCharset: 'utf8',
            outCharset: 'utf-8',
            notice: 0,
            platform: 'yqq',
            needNewCode: 0
          },
          jsonp: 'jsonpCallback'
        }).then((response) => {
          this.searchRes = response.data.data
          return response.data.data;
        })
      },
      play(data){
        this.$parent.showW = 'ok';
        this.$parent.songInfo = {name:data.name,songer:data.singer,lyArr:[],num:0,lyTop:0,id:data.id};
      },
      toPlay(){
        this.$parent.showW = 'ok';
      }
  }
}
</script>
<style scoped>
.my-list{
  opacity: 0.8;
}
.my-list .panel-body{
  color: rgb(118, 133, 110);
  height: 70px;
  font: 25px/40px arial;
}
.my-list .panel-body + .panel-body{
  border-top: 6px #faf1f1 solid;
  
}
</style>