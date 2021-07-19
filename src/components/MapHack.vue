<template>
  <div ref="map" class="map">
    <div class="realmap">
      <ul>
        <ol
          v-for="(item, index) in heroData"
          :key="index"
          class="hero"
          :style="{
            backgroundImage:
              'url(' +
              `http://game.gtimg.cn/images/yxzj/img201606/heroimg/${item.id}/${item.id}.jpg` +
              ')',
            top: getxy(item).top + 'px',
            left: getxy(item).left + 'px',
          }"
        >
        <span class="bloodtext">{{Math.floor(item.b)}}</span>
        <div class="bloodwrap"><div class="blood"  :style="{height:getHeight(item),backgroundColor:backgroundColor(item)}"></div> </div><div class="ult">{{item.u}}</div> </ol>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Maphack",
  props: {
    msg: String,
  },
  data() {
    return {
      wsurl: "ws://127.0.0.1:8887",
      chart: null,
      socket: null,
      heroData: {
             },
    };
  },
  beforeDestroy() {
    if (this.socket) {
      this.socket.close();
    }
  },
  mounted() {
    //this.initMap();
    this.initWebsocket();
  },
  methods: {
    initMap() {
      this.chart = echarts.init(this.$el, "macarons");
      this.chart.setOption();
    },
    initWebsocket() {
      if (this.socket) {
        this.socket.close();
      }
      this.socket = new WebSocket(this.wsurl);
      this.socket.onopen = this.openMapMsg;
      this.socket.onmessage = this.getMapMsg;
    },
    openMapMsg() {},
    getMapMsg(msg) {
        const mapInfo = eval('(' + msg.data + ')')
        if (mapInfo.id) {
          this.renderHero(mapInfo)
        }
    },
    renderHero(hero) {
      let sin45= Math.sin(Number((45 * Math.PI) / 180));
      let cos45= Math.cos(Number((45 * Math.PI) / 180));
      let realx= hero.mx * sin45
      let realy= hero.my * cos45
      let mapx = (realx+51100)*550/102200
      let mapy = (102200-(realy+51100))*550/102200
     
      this.$set(this.heroData, hero.id, {
        ...hero,
        ...{ top: mapy, left: mapx },
      });
    },
    getxy(hero){
      let sin45= Math.sin(Number((45 * Math.PI) / 180));
      let cos45= Math.cos(Number((45 * Math.PI) / 180));
      let realx= hero.mx * sin45
      let realy= hero.my * cos45
       let mapx = (realx+51100)*700/102200
      let mapy = (102200-(realy+51100))*700/102200
      return {top: mapy-20, left: mapx-20}
    },
    getHeight(hero){
     let height =Math.floor( hero.b*40/100)
     return `${height}px`
    },
    backgroundColor(hero){
      let blood = hero.b
      let color = ""
      if(blood<=25){
        color="rgb(0,255,0)"
      }else if(25< blood && blood<=50 ){
        color="rgb(255,140,0)"
      }else if(50< blood && blood<=70 ){
        color="rgb(255,255,0)"
      }else if(75< blood && blood<=100 ){
        color="rgb(0,255,0)"
      }
   
      return color
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  scoped>
.map {
   position: absolute;
  height: 700px;
  width: 700px;
  background-image: url("../assets/map.jpg");
  background-repeat: no-repeat;
  background-size:100%  100%;
}
.realmap {
    height:700px;
    width: 700px;
    position: absolute;
    top: 0px;
    left: 0px;
  }
ul,
li {
  padding: 0;
  margin: 0;
  list-style: none;
}
.hero {
  position: absolute;
  display: block;
  height: 40px;
  width: 40px;
  border-radius: 40px;
  padding-inline-start: 0;
  background-size: cover;
  background-position-x: 0px;
  background-position-y: 0px;
}
.bloodwrap{
  position: absolute;
  display: block;
  height: 40px;
  width: 5px;
  left:-10px;
  
  border: 1px solid greenyellow;
  /* background-color: chartreuse; */
  
}
.blood{
   position: absolute;
   bottom: 0px;
   width: 5px;
}
.bloodtext{
  font-size: 10px;
  color:crimson;
  position: absolute;
  top:-15px;
  left:-15px;
}
.ult{
 position: absolute;
  display: none;
  height: 40px;
  width: 10px;
  right:-10px;
  background-color: chartreuse;
  
}
</style>
