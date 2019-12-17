<template>
  <div class="home">
    <div v-for="(item, index) in list" :key="index">
      <van-card
        num="1"
        :origin-price="item.originPrice"
        :price="item.money"
        :desc="'剩余'+item.num+'件'"
        :title="item.name"
        thumb="https://img.yzcdn.cn/vant/t-thirt.jpg"
      >
        <div slot="bottom">
    
          <van-count-down
          :time="item.time"
          format="倒计时: DD 天 HH 时 mm 分 ss 秒"
          v-show="item.flag"
          @finish="request"
        />

        </div>
        <div slot="footer">
          <van-button @click="commit(item)" round type="info" :disabled="item.bFlag" size="normal">{{item.butText}}</van-button>
        </div>
      </van-card>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";

export default {
  data(){
    return{
      list: {},
      time: 0,
      id: 1,
      demo: 0,
    }
  },
  name: 'home',
  components: {
    
  },
  methods: {
    request(){
      axios.request({
        url: '/product/seckill',
        method: 'get',
        baseURL: 'http://localhost:8081',

      }).then((res)=>{
        this.list = res.data.data
        for(var i in res.data.data){
          this.list[i].time=(Date.parse(this.list[i].startTime)-Date.parse(new Date()))
          if(this.list[i].status==0){
            this.list[i].butText = "抢购还没有开始"
            this.list[i].flag = true
            this.list[i].bFlag = true
          }else if(this.list[i].status==1){
            this.list[i].flag = false
            this.list[i].bFlag = false
            this.list[i].butText = "立即下单"
          }else if(this.list[i].status==2){
            this.list[i].flag = false
            this.list[i].bFlag = true
            this.list[i].butText = "抢购已经结束"
          }
          this.$forceUpdate();

        }

        
      })
    },
    commit(item){
      this.id =Math.floor((Math.random())*10000)
      this.demo++;
      axios.request({
        url: '/product/getone',
        method: 'POST',
        baseURL: 'http://localhost:8081',
        data: {
          id: item.id,
          userId: this.id,
        }
      }).then((res)=>{
        console.log(res.data.data)
        if(res.data.data=="已经抢空了"){
          this.request();
        }
      })

      for(;this.demo<100;){
        this.commit(item)
      }
    }
  },
  mounted(){
    this.request();
  },


}
</script>

<style lang="scss" scoped>

</style>