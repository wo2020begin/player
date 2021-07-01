<template>
  <div class="playlists-container">
    <!-- 同步 -->
    <div class="top-card">
      <div class="icon-wrap">
        <img :src="topList.coverImgUrl" alt="" />
      </div>
      <div class="content-wrap">
        <div class="tag">精品歌单</div>
        <div class="title">{{ topList.name }}</div>
        <div class="info">{{ topList.description }}</div>
      </div>
      <img :src="topList.coverImgUrl" alt="" class="bg" />
      <div class="bg-mask"></div>
    </div>
    <div class="tab-container">
      <!-- tab栏 顶部 -->
      <div class="tab-bar">
        <span class="item" v-for="(item,index) in tagList" :key='index' :class="{ active: tag === item }" @click="tag = item">
          {{item}}</span>
      </div>
      <!-- tab的内容区域 -->
      <div class="tab-content">
        <div class="items">
          <div class="item" v-for="(item, index) in list" :key="index">
            <div class="img-wrap">
              <div class="num-wrap">
                播放量:<span class="num">{{ item.playCount }}</span>
              </div>
              <img :src="item.coverImgUrl" alt="" />
              <span class="iconfont icon-play"></span>
            </div>
            <p class="name">{{ item.name }}</p>
          </div>
        </div>
      </div>
    </div>
    <!-- 分页器 -->
    <el-pagination
      @current-change="handleCurrentChange"
      background
      layout="prev, pager, next"
      :total="total"
      :current-page="page"
      :page-size="10"
    >
    </el-pagination>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "recommend",
  data() {
    return {
      // 总条数
      total: 0,//data.total
      // 页码
      page: 1,
      topList: {},
      list: [],
      tagList:['全部','欧美','华语','流行','说唱','摇滚','民谣','电子','轻音乐','影视原声','ACG','怀旧','治愈','旅行'],
      tag: "全部",
    };
  },
  methods: {
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.page=val;
      this.listData();
    },
    // 将多次用到的方法抽取出来
    // 方法1
    topData(){
      axios({
        url: "https://autumnfish.cn/top/playlist/highquality",
        methods: "get",
        params: {
          limit: 1,
          cat: this.tag,
        },
      }).then((res) => {
        console.log(res);
        this.topList = res.data.playlists[0];
      })
    },
    //方法2
    listData(){
      axios({
        url: "https://autumnfish.cn/top/playlist/",
        methods: "get",
        params: {
        limit: 10,
        cat: this.tag,
        offset: (this.page-1)*10,
        },
      }).then((res) => {
        console.log(res);
        this.list = res.data.playlists;
        this.total=res.data.total
      });
    }   
  },
  watch: {
    tag() {
      this.page=1;
      console.log(this.tag);
      this.topData();
      this.listData();
    }
  },
  created() {
    this.topData();
    this.listData();
  }
};
</script>

<style >
</style>
