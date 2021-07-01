<template>
  <div class="songs-container">
    <div class="tab-bar">
      <span class="item" v-for="(item,index) in area" :key="index" @click="tag=item.id" :class="{active:tag===item.id}">
        {{item.name}}</span>
    </div>
    <!-- 底部的table -->
    <table class="el-table playlit-table">
      <thead>
        <th></th>
        <th></th>
        <th>音乐标题</th>
        <th>歌手</th>
        <th>专辑</th>
        <th>时长</th>
      </thead>
      <tbody>
        <tr class="el-table__row" v-for="(item,index) in lists" :key='index'>
          <td>{{index+1}}</td>
          <td>
            <div class="img-wrap">
              <img :src="item.album.picUrl" alt="" />
              <span class="iconfont icon-play" @click="playMusic(item.id)"></span>
            </div>
          </td>
          <td>
            <div class="song-wrap">
              <div class="name-wrap">
                <span>{{item.name}}</span>
                <span class="iconfont icon-mv"></span>
              </div>
              <span>{{item.album.company}}</span>
            </div>
          </td>
          <td>{{item.album.artists[0].name}}</td>
          <td>{{item.album.name}}</td>
          <td>{{item.duration}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'songs',
  data() {
    return {
      lists:[],
      tag:0,
      area:[
        {name:'全部',id:0},
        {name:'华语',id:7},
        {name:'欧美',id:96},
        {name:'日本',id:8},
        {name:'韩国',id:16},
      ]
    };
  },
  methods:{
    getList(){
      axios({
        url:'https://autumnfish.cn/top/song',
        methods:'get',
        params:{
          type:this.tag
        }
      }).then(res=>{
        console.log(res)
        this.lists=res.data.data
        for(let i=0;i<this.lists.length;i++){
          let duration=this.lists[i].duration
          let min=parseInt(duration/1000/60)
          if(min<10){
            min='0'+min
          }
          let sec=parseInt(duration/1000%60)
          if(sec<10){
            sec='0'+sec
          }
          this.lists[i].duration=`${min}:${sec}`
        }
      })
    },
    playMusic(id){
      axios({
        url:'https://autumnfish.cn/song/url',
        methods:'get',
        params:{
          id
        }
      }).then(res=>{
        console.log(res)
        let url=res.data.data[0].url
        this.$parent.musicUrl=url
      })
    }
  },
  watch:{
    tag(){
      this.getList();
    }
  },
  created(){
    this.getList();
  }
};
</script>

<style >

</style>
