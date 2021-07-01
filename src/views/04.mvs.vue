<template>
  <div class="mvs-container">
    <div class="filter-wrap">
      <div class="seciton-wrap">
        <span class="section-type">地区:</span>
        <ul class="tabs-wrap">
          <li class="tab" v-for="(item,index) in areas" :key="index">
            <span class="title" @click="area=item" :class="{active:area===item}">{{item}}</span>
          </li>
        </ul>
      </div>
      <div class="type-wrap">
        <span class="type-type">类型:</span>
        <ul class="tabs-wrap">
          <li class="tab" v-for="(item,index) in types" :key="index">
            <span class="title" @click="type=item" :class="{active:type===item}">{{item}}</span>
          </li>
        </ul>
      </div>
      <div class="order-wrap">
        <span class="order-type">排序:</span>
        <ul class="tabs-wrap">
          <li class="tab" v-for="(item,index) in orders" :key="index">
            <span class="title" @click="order=item" :class="{active:order===item}">{{item}}</span>
          </li>
        </ul>
      </div>
    </div>
    <!-- mv容器 -->
    <!-- 推荐MV -->
    <div class="mvs">
      <div class="items">
        <div class="item" v-for="(item,index) in list" :key="index">
          <div class="img-wrap">
            <img :src="item.cover" alt="" />
            <div class="num-wrap">
              <div class="iconfont icon-play"></div>
              <div class="num">{{item.playCount}}</div>
            </div>
          </div>
          <div class="info-wrap">
            <div class="name">{{item.name}}</div>
            <div class="singer">{{item.artistName}}</div>
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
        :page-size="5"
        :limit="limit"
      >
      </el-pagination>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'mvs',
  data() {
    return {
      // 总条数
      total: 20,
      // 页码
      page: 1,
      // 页容量
      limit: 8,
      area:'全部',
      areas:['全部','内地','港台','欧美','日本','韩国'],
      type:'全部',
      types:['全部','官方版','原声','现场版','网易出品'],
      order:'上升最快',
      orders:['上升最快','最热','最新'],
      list:[]
    };
  },
  methods: {
    toMv(id) {
      this.$router.push(`/mv?id=${id}`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.page=val
      this.getList()
    },
    getList(){
      axios({
        url:'https://autumnfish.cn/mv/all',
        methods:'get',
        params:{
          area:this.area,
          type:this.type,
          order:this.order,
          limit:this.limit,
          offset:(this.page-1)*this.limit
        }
      }).then(res=>{
        console.log(res)
        this.list=res.data.data;
        for(let i=0;i<this.list.length;i++){
          if(this.list[i].playCount>100000){
            this.list[i].playCount=parseInt(this.list[i].playCount/10000)+'万'
          }
        }
        if(res.data.count){
          this.total=res.data.count
        }
      })
    }
  },
  watch:{
    area(){
      this.page=1;
      this.getList();
    },
    type(){
      this.page=1;
      this.getList();
    },
    order(){
      this.page=1;
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
