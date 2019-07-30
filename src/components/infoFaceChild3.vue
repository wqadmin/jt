<template>
  <div class="infofacechild3">
    <div class="info_face_left" ref="infoFaceC3">
      <div id="main" @click="infoC3CrossLineFun"></div>
    </div>
    <div class="info_face_right">
      <h4>{{newInfoFace.name}}（{{newInfoFace.code}}）</h4>
      <ul class="info_face_right_sell">
        <li>
          <p class="info_face_right_green">卖出</p>
          <p>{{infoFiveSellPrice}}</p>
          <p>{{infoFiveSellNum}}</p>
        </li>
      </ul>
      <ul class="info_face_right_buy">
        <li>
          <p class="info_face_right_red">买入</p>
          <p>{{infoFiveBuyPrice}}</p>
          <p>{{infoFiveBuyNum}}</p>
        </li>
      </ul>
      <ul class="info_face_right_new">
        <li>
          <p>最新</p>
          <p
            :class="newInfoFace.change_rate<0?'info_face_right_green':'info_face_right_red'"
          >{{newInfoFace.current}}</p>
          <p>涨跌</p>
          <p
            :class="newInfoFace.change_rate<0?'info_face_right_green':'info_face_right_red'"
          >{{newInfoFace.change}}</p>
        </li>
        <li>
          <p>现手</p>
          <p class="info_face_right_orange">{{newInfoFace.now_hand}}</p>
          <p>幅度</p>
          <p
            :class="newInfoFace.change_rate<0?'info_face_right_green':'info_face_right_red'"
          >{{newInfoFace.change_rate}}</p>
        </li>
        <li>
          <p>总手</p>
          <p class="info_face_right_orange">{{newInfoFace.total_hand}}</p>
          <p>开盘</p>
          <p>{{newInfoFace.open}}</p>
        </li>
        <li>
          <p>持仓</p>
          <p class="info_face_right_orange">{{newInfoFace.turnover}}</p>
          <p>最高</p>
          <p
            :class="newInfoFace.change_rate<0?'info_face_right_green':'info_face_right_red'"
          >{{newInfoFace.high}}</p>
        </li>
        <li>
          <p>昨结</p>
          <p>{{newInfoFace.p_clear}}</p>
          <p>最低</p>
          <p
            :class="newInfoFace.change_rate<0?'info_face_right_green':'info_face_right_red'"
          >{{newInfoFace.low}}</p>
        </li>
      </ul>
      <ul class="info_face_right_time">
        <li>
          <p>时间</p>
          <p>价格</p>
          <p>现手</p>
        </li>
        <li v-for="timePriceHand in timePriceHands" :key="timePriceHand.id">
          <p>{{timePriceHand.time}}</p>
          <p>{{timePriceHand.price}}</p>
          <p class="info_face_right_orange">{{timePriceHand.volume}}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import echarts from "echarts";
import { makeTimeLine2 } from "../assets/js/timeLine.js";
export default {
  name: "InfoFaceChild3",
  components: {},
  data() {
    return {
      newInfoFaceSellFiveLists: [
        // 五档线卖出
        { id: 1, name: "卖五", color: false },
        { id: 2, name: "卖四", color: false },
        { id: 3, name: "卖三", color: false },
        { id: 4, name: "卖二", color: false },
        { id: 5, name: "卖出", color: true }
      ],
      newInfoFaceBuyFiveLists: [
        // 五档线卖出
        { id: 1, name: "买入", color: true },
        { id: 2, name: "买二", color: false },
        { id: 3, name: "买三", color: false },
        { id: 4, name: "买四", color: false },
        { id: 5, name: "买五", color: false }
      ],
      infoFiveSellPrice: "", // 五档卖出
      infoFiveSellNum: "", // 五档卖出
      infoFiveBuyPrice: "", // 五档买入
      infoFiveBuyNum: "", // 五档买入
      newInfoFace: "", // 最新涨跌中间信息
      timePriceHands: "", // 时间价格现手
      timeKlineFun: "", // 调用分时函数
      infoChangeTimeKlineWHFun: "", // 改变分时宽高的函数
      InfoKlineCode: "", // 合约名字调用分时时用
      infoC3GetTimer: "", // 分时计时器
      infoC3GetTimerBoole: true, // 分时计时器暂停
      dataData: "",
      isCrossLine: false, // 分时十字线显隐
      websock: null, // 推送
      websocketBoolean: true, // 手动断开
      infoC3WebSocketTimer: "" // 推送计时器
    };
  },
  created() {},
  activated() {
    let that = this;
    this.initWebSocket(); // 开启推送
    window.onbeforeunload = function() {
      // 页面刷新
      sessionStorage.setItem("timeLine", false);
    };
    that.infoC3GetTimerBoole = true;
    that.timeKlineFun = function(codeName) {
      that
        .$http({
          url: "/",
          method: "post",
          timeout: 3000,
          data: {
            nozzle: "time_sharing",
            code: codeName
          }
        })
        .then(res => {
          if (res.data.code == 1) {
            // clearInterval(this.infoC3GetTimer);
            // 分时的小数位
            sessionStorage.setItem("tfC3PointNum", res.data.data.data.price); // 小数位
            var response = res.data.data;
            var lastIndex = response.trading_time.length - 1;
            var endTime = response.trading_time[lastIndex].substring(6, 12); //最后一条时间
            var arr = [];
            var hh, mm, hhmm;
            var trade_time = that.getTimes(response.trading_time);
            $.each(response.time, function(index, item) {
              if (index == 0) {
                item.close = response.data.p_close;
              }
              hh = item.time.slice(0, 2); // 小时
              mm = item.time.slice(2, 4); // 分钟
              hhmm = hh + ":" + mm;
              var str = item.time + " " + item.close + " " + item.volume;
              arr.push(str);
              // for (let i = 0; i < trade_time.length; i++) {
              //   if(hhmm == trade_time[i]) {
              //     var str = item.time + " " + item.close + " " + item.volume;
              //     arr.push(str);
              //   }
              // }
            });
            makeTimeLine2(arr, response.data, trade_time, endTime);
            that.dataData = res.data;
            that.infoFiveSellPrice = res.data.data.five_gear.ask[4].price; // 五档卖出
            that.infoFiveSellNum = res.data.data.five_gear.ask[4].number; // 五档卖出
            that.infoFiveBuyPrice = res.data.data.five_gear.bid[0].price; // 五档买入
            that.infoFiveBuyNum = res.data.data.five_gear.bid[0].number; // 五档买入
            that.newInfoFace = res.data.data.data; // 最新涨跌中间信息
            that.timePriceHands = res.data.data.tick; // 时间价格现手
            // if (that.infoC3GetTimerBoole) {
            //   // console.log("C3");
            //   that.infoC3GetTimer = setInterval(() => {
            //     that.timeKlineFun(that.$store.state.codeName);
            //   }, 1000);
            // }
            window.myChart.resize(); // 调整分时的宽高
          } else if (res.data.code == 0 || res.data.code == -1) {
            that.$message.error(res.data.msg);
          }
        });
    };
    // that.timeKlineFun(that.$store.state.codeName);
  },
  mounted: function() {
    let that = this;
    // that.InfoKlineCode = that.$store.state.codeName; // 合约名字

    $(window).resize(function() {
      window.myChart.resize();
    });
  },
  methods: {
    initWebSocket() {
      //初始化weosocket
      const wsuri = "ws://47.52.155.199:1314";
      this.websock = new WebSocket(wsuri);
      this.websock.onmessage = this.websocketonmessage;
      this.websock.onopen = this.websocketonopen;
      this.websock.onerror = this.websocketonerror;
      this.websock.onclose = this.websocketclose;
    },
    websocketonopen() {
      //连接建立之后执行send方法发送数据
      let that = this;
      let actions = {
        nozzle: "time_sharing",
        code: that.$store.state.codeName
      };
      this.websocketsend(JSON.stringify(actions));
      clearInterval(that.infoC3WebSocketTimer); // 推送计时器
      that.infoC3WebSocketTimer = setInterval(() => {
        that.websocketsend(JSON.stringify(actions));
      }, 2000);
    },
    websocketonerror() {
      //连接建立失败重连
      setTimeout(() => {
        this.websock.close(); //离开路由之后断开websocket连接
        this.initWebSocket();
        if (navigator.onLine) {
          // console.log("重连 有.... 网");
          this.websocketBoolean = true;
        } else {
          // console.log("重连 没 网");
          this.$message.error("请查看您的网络连接");
          this.websocketBoolean = false;
        }
      }, 20000);
    },
    websocketsend(Data) {
      //数据发送
      this.websock.send(Data);
    },
    websocketonmessage(e) {
      //数据接收
      let that = this;
      if (e.data.slice(0, 4) != "连接成功") {
        const redata = JSON.parse(e.data);
        sessionStorage.setItem("tfC3PointNum", redata.data.data.price); // 小数位
        var response = redata.data;
        var lastIndex = response.trading_time.length - 1;
        var endTime = response.trading_time[lastIndex].substring(6, 12); //最后一条时间
        var arr = [];
        var hh, mm, hhmm;
        var trade_time = that.getTimes(response.trading_time);

        $.each(response.time, function(index, item) {
          if (index == 0) {
            item.close = response.data.p_close;
          }
          hh = item.time.slice(0, 2); // 小时
          mm = item.time.slice(2, 4); // 分钟
          hhmm = hh + ":" + mm;
          var str = item.time + " " + item.close + " " + item.volume;
          arr.push(str);
          // for (let i = 0; i < trade_time.length; i++) {
          //   if(hhmm == trade_time[i]) {
          //     var str = item.time + " " + item.close + " " + item.volume;
          //     arr.push(str);
          //   }
          // }
        });
        makeTimeLine2(arr, response.data, trade_time, endTime);
        that.dataData = redata.data;
        that.infoFiveSellPrice = redata.data.five_gear.ask[4].price; // 五档卖出
        that.infoFiveSellNum = redata.data.five_gear.ask[4].number; // 五档卖出
        that.infoFiveBuyPrice = redata.data.five_gear.bid[0].price; // 五档买入
        that.infoFiveBuyNum = redata.data.five_gear.bid[0].number; // 五档买入
        that.newInfoFace = redata.data.data; // 最新涨跌中间信息
        if (redata.data.tick.length != 0) {
          that.timePriceHands = redata.data.tick; // 时间价格现手
        }
        window.myChart.resize(); // 调整分时的宽高
      }
    },
    websocketclose(e) {
      //关闭
      // console.log('断开连接',e);
      let that = this;
      setTimeout(() => {
        if (that.websocketBoolean) {
          that.websock.close(); //离开路由之后断开websocket连接
          that.initWebSocket();
        }
        if (navigator.onLine) {
          // console.log("断开后有网");
          that.websocketBoolean = true;
        } else {
          // console.log("断开后没网");
          that.websocketBoolean = false;
        }
      }, 2000);
    },

    infoC3CrossLineFun() {
      // 十字线显隐
      this.isCrossLine = !this.isCrossLine;
      if (this.isCrossLine) {
        sessionStorage.setItem("timeLine", true); // 分时十字线显示
      } else {
        sessionStorage.setItem("timeLine", false); // 分时十字线隐藏
      }
      this.timeKlineFun(this.$store.state.codeName);
    },
    getTimes(range) {
      let that = this;
      var today = new Date(new Date().setHours(0, 0, 0, 0)) / 1000;
      var array = [];
      //0:"06:00-24:00"1:"00:00-05:00"
      // 0: "17:15-24:00"
      // 1: "00:00-03:00"
      // 2: "09:15-12:00"
      // 3: "13:00-16:30"
      $.each(range, function(index, item) {
        item = item.split("-");
        var data = [];
        for (var i = 0; i < item.length; i++) {
          var hour = item[i].substring(0, 2);
          var minute = item[i].substring(3, 5);
          var time_one = today + hour * 3600 + minute * 60;
          data.push(time_one);
        }
        array = that.time_array_new(data[0], data[1], array);
      });
      var arrays = [];
      for (var x = 0; x < array.length; x++) {
        arrays.push(that.time_data(array[x]));
      }
      return arrays;
    },
    time_array_new($start, $end, array) {
      var difference = ($end - $start) / 60;
      for (var i = 0; i < difference; i++) {
        array.push($start + i * 60);
      }
      return array;
    },
    time_data(time) {
      let that = this;
      var amDate = new Date(time * 1000);
      return (
        that.getzf(amDate.getHours()) + ":" + that.getzf(amDate.getMinutes())
      );
    },
    getzf(num) {
      if (parseInt(num) < 10) {
        num = "0" + num;
      }
      return num;
    }
  },
  beforeRouteLeave(to, from, next) {
    //切换路由 离开页面清除计时器
    clearInterval(this.infoC3GetTimer);
    this.infoC3GetTimerBoole = false; // 计时器清除

    next();
  },
  deactivated() {
    sessionStorage.setItem("timeLine", false); // 离开十字线隐藏
    this.isCrossLine = false; // 离开十字线隐藏

    this.websocketBoolean = false;
    this.websock.close(); //离开路由之后断开websocket连接
    clearInterval(this.infoC3WebSocketTimer); // 推送计时器
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#main {
  width: 100%;
  height: 100%;
}
.infofacechild3 {
  width: 100%;
  height: 100%;
  color: white;
  font-size: 0;
  display: flex;
  display: -webkit-flex;
  flex-direction: row;
  -webkit-flex-direction: row;
  justify-content: space-between;
  -webkit-justify-content: space-between;
  align-items: center;
  -webkit-align-items: center;
}
.info_face_left {
  display: inline-block;
  width: -webkit-calc(100% - 270px);
  width: calc(100% - 270px);
  height: 100%;
  font-size: 14px;
  background-color: rgb(0, 0, 0);
  vertical-align: top;
}
.info_face_right {
  display: inline-block;
  width: 270px;
  height: 100%;
  border-left: 1px solid #990e0e;
  background-color: rgba(0, 0, 0, 1);
  font-size: 14px;
  vertical-align: top;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.info_face_right > h4 {
  margin: auto;
  padding: 0;
  border-bottom: 1px solid #990e0e;
  line-height: 29px;
  color: rgba(255, 152, 0, 1);
  font-size: 14px;
  font-weight: 500;
  text-align: center;
}
.info_face_right > ul {
  margin: auto;
  border-bottom: 1px solid #990e0e;
  padding: 4px 0;
}
.info_face_right > .info_face_right_time {
  border: 0 none;
}
.info_face_right > ul > li {
  display: flex;
  display: -webkit-flex;
  flex-direction: row;
  -webkit-flex-direction: row;
  justify-content: space-between;
  -webkit-justify-content: space-between;
  align-items: center;
  -webkit-align-items: center;
}
.info_face_right > ul > li > p {
  width: 26.3%;
  text-align: center;
  line-height: 20px;
  color: rgba(255, 255, 255, 1);
  font-size: 14px;
}
.info_face_right > .info_face_right_new > li > p {
  width: 25%;
  text-align: center;
}
.info_face_right > .info_face_right_new > li > p:nth-child(2) {
  text-align: right;
}
.info_face_right > .info_face_right_new > li > p:nth-child(4) {
  text-align: right;
  padding-right: 13px;
}
.info_face_right > .info_face_right_sell > li > .info_face_right_green {
  color: #259b24;
}
.info_face_right > .info_face_right_sell > li > p:nth-child(2) {
  color: #e51c23;
}
.info_face_right > .info_face_right_buy > li > p:nth-child(2) {
  color: #e51c23;
}
.info_face_right > .info_face_right_buy > li > .info_face_right_red {
  color: #e51c23;
}
.info_face_right > .info_face_right_sell > li > p:nth-child(3) {
  color: #ff9800;
}
.info_face_right > .info_face_right_buy > li > p:nth-child(3) {
  color: #ff9800;
}

.info_face_right > ul > li > .info_face_right_orange {
  color: #ff9800;
}

.info_face_right > .info_face_right_new > li > .info_face_right_orange {
  color: #ff9800;
}
.info_face_right > .info_face_right_new > li > .info_face_right_red {
  color: #e51c23;
}
.info_face_right > .info_face_right_new > li > .info_face_right_green {
  color: #259b24;
}
</style>