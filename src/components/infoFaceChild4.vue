<template>
  <div class="infofacechild4" ref="infoFaceC4">
    <div class="info_face_left" ref="infoFaceLeft">
      <div @click.right.capture="infoC2RightMenuFun()" id="kline_container" ref="klineContainer"></div>
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
    <!-- 鼠标右键 -->
    <div
      id="chart_tabbar"
      v-clickoutside="handleCloseTfC2Menu"
      class="tf_right_menu"
      v-show="isShowTfC2Menu"
      v-bind:style="{ top: activeTopMenu + 'px', left: activeLeftMenu + 'px' }"
    >
    <ul class="tf_right_menu_ul">
      <li
        class="tf_right_menu_li"
        @click="infoC2MenuChooseFun(menuList.id)"
        v-for="menuList in menuLists"
        :key="menuList.id"
        :class="{tf_right_menu_li_active:menuList.isClicked == true}"
      >
        <a :name="menuList.name">{{menuList.name}}</a>
      </li>
    </ul>
  </div>
  </div>
</template>
<script>
import echarts from "echarts";
import Kline from "kline";
import "../assets/js/jquery.min.js";
import "../assets/js/jquery.mousewheel.js";
const clickoutside = {
  // 初始化指令
  bind(el, binding, vnode) {
    function documentHandler(e) {
      // 这里判断点击的元素是否是本身，是本身，则返回
      if (el.contains(e.target)) {
        return false;
      }
      // 判断指令中是否绑定了函数
      if (binding.expression) {
        // 如果绑定了函数 则调用那个函数，此处binding.value就是handleClose方法
        binding.value(e);
      }
    }
    // 给当前元素绑定个私有变量，方便在unbind中可以解除事件监听
    el.__vueClickOutside__ = documentHandler;
    document.addEventListener("click", documentHandler);
  },
  update() {},
  unbind(el, binding) {
    // 解除事件监听
    document.removeEventListener("click", el.__vueClickOutside__);
    delete el.__vueClickOutside__;
  }
};
export default {
  name: "InfoFaceChild4",
  data() {
    return {
      isShowTfC2Menu: false, // 右键菜单默认隐藏
      activeTopMenu: 0, // 初始位置
      activeLeftMenu: 0, // 初始位置
      menuLists: [
        {id: 1, name: "MACD"},
        {id: 2, name: "KDJ"},
        {id: 3, name: "StochRSI"},
        {id: 4, name: "RSI"},
        {id: 5, name: "DMI"},
        {id: 6, name: "OBV"},
        {id: 7, name: "BOLL"},
        {id: 8, name: "SAR"},
        {id: 9, name: "DMA"},
        {id: 10, name: "TRIX"},
        {id: 11, name: "BRAR"},
        {id: 12, name: "VR"},
        {id: 13, name: "EMV"},
        {id: 14, name: "WR"},
        {id: 15, name: "ROC"},
        {id: 16, name: "MTM"},
        {id: 17, name: "PSY"},
      ],
      newInfoFaceSellFiveLists: [ // 五档线卖出
        {id: 1, name: "卖五", color: false},
        {id: 2, name: "卖四", color: false},
        {id: 3, name: "卖三", color: false},
        {id: 4, name: "卖二", color: false},
        {id: 5, name: "卖出", color: true},
      ],
      newInfoFaceBuyFiveLists: [ // 五档线卖出
        {id: 1, name: "买入", color: true},
        {id: 2, name: "买二", color: false},
        {id: 3, name: "买三", color: false},
        {id: 4, name: "买四", color: false},
        {id: 5, name: "买五", color: false},
      ],
      infoFiveSellPrice: '', // 五档卖出
      infoFiveSellNum: '', // 五档卖出
      infoFiveBuyPrice: '', // 五档买入
      infoFiveBuyNum: '', // 五档买入
      newInfoFace: "", // 最新涨跌中间信息
      timePriceHands: "", // 时间价格现手
      infoC4RightFaceFun: "", // 定义右侧面板的函数
      infoC4GetKlineFun: "", // 调用K线函数
      infoChangeKlineWHFun: "", // 改变K线宽高的函数
      klineWidth: "", // K线宽
      klineHeight: "", // K线高
      klineUrl: "", // K线 url 传参
      InfoKlineCode: "", // 合约名字调用K线时用
      infoC4GetTimer: "", // 计时器
      infoC4GetTimerBoole: true, // 计时器暂停
      kline: "",
      period: "100",
      websock: null, // 推送
      websocketBoolean: true, // 手动断开
      infoC4WebSocketTimer: "", // 推送计时器
    };
  },
  watch: {},
  created() {
    this.initWebSocket(); // 开启推送
  },
  mounted() {
    let that = this;
    that.infoC4RightFaceFun = function(index) {
      //获取交易所合约内容信息
      // console.log(index);
      that
        .$http({
          url: "/",
          method: "post",
          timeout: 3000,
          data: {
            nozzle: "kline_sharing",
            code: index
          }
        })
        .then(res => {
          if (res.data.code == 1) {
            // clearInterval(that.infoC4GetTimer);
            sessionStorage.setItem("drawNumPoint", res.data.data.data.price); // 小数位
            that.infoFiveSellPrice = res.data.data.five_gear.ask[4].price;// 五档卖出
            that.infoFiveSellNum = res.data.data.five_gear.ask[4].number;// 五档卖出
            that.infoFiveBuyPrice = res.data.data.five_gear.bid[0].price;// 五档买入
            that.infoFiveBuyNum = res.data.data.five_gear.bid[0].number;// 五档买入
            that.newInfoFace = res.data.data.data; // 最新涨跌中间信息
            that.timePriceHands = res.data.data.tick; // 时间价格现手
            // if (that.infoC4GetTimerBoole) {
            //   that.infoC4GetTimer = setInterval(() => {
            //     // console.log("C4");
            //     that.infoC4RightFaceFun(that.$store.state.codeName); // 获取合约信息
            //   }, 1000);
            // }
          } else if (res.data.code == 0 || res.data.code == -1) {
            that.$message.error(res.data.msg);
          }
        });
    };
    that.infoC4RightFaceFun(that.$store.state.codeName); // 交易所合约信息初始化
    
    
    that.infoC4GetKlineFun = function(codeName, time) {
      // 调用K线
      // console.log(codeName, time);
      clearInterval(that.infoC4GetTimer);
      
      window.sessionStorage.setItem("index", 1);
      window.sessionStorage.setItem("short", codeName); // 存下合约名画线下单用
      let kUrl = {
        nozzle: "kline_sharing",
        code: codeName,
        period: time
      }
      that.klineWidth = $(".info_face_left").width();
      that.klineHeight = $(".info_face_left").height();
      that.kline = new Kline({
        element: "#kline_container",
        width: that.klineWidth,
        height: that.klineHeight,
        theme: "dark", // light/dark
        language: "zh-cn", // zh-cn/en-us/zh-tw
        // ranges: ["1w", "1d", "1h", "30m", "15m", "5m", "1m", "line"],
        ranges: ["1w", "1d", "1h", "30m", "15m", "5m", "1m"],
        symbol: "",
        symbolName: "COIN5_COIN4",
        type: "poll", // poll/socket
        // url: "http://hrserve.htcrm.net/?nozzle=kline_sharing&code="+dbInfoKlineCode+"&period=001",
        url: kUrl,
        limit: 1000,
        intervalTime: 5000,
        debug: false,
        showTrade: false,
        onResize: function(width, height) {}
      });
      that.kline.draw();
    };
    that.infoC4GetKlineFun(that.$store.state.codeName, that.period); // K线初始化

    
    that.infoChangeKlineWHFun = function() {
      // K线的宽高适应
      that.$nextTick(() => {
        that.klineWidth = $(".info_face_left").width();
        that.klineHeight = $(".info_face_left").height();
        that.kline.resize(that.klineWidth, that.klineHeight);
      })
    };
    that.$nextTick(()=>{
      window.addEventListener('resize', function() {// K线自适应
        if (that.$route.path == "/wrap/infoFace/infoFaceChild4") {
          that.klineWidth = $(".info_face_left").width();
          that.klineHeight = $(".info_face_left").height();
          that.kline.resize(that.klineWidth, that.klineHeight);
        }
      });
    });
  },
  directives: { clickoutside }, //自定义指令点击空白右键消失
  methods: {
    initWebSocket(){ //初始化weosocket
      const wsuri = "ws://47.52.155.199:1314";
      this.websock = new WebSocket(wsuri);
      this.websock.onmessage = this.websocketonmessage;
      this.websock.onopen = this.websocketonopen;
      this.websock.onerror = this.websocketonerror;
      this.websock.onclose = this.websocketclose;
    },
    websocketonopen(){ //连接建立之后执行send方法发送数据
      let that = this;
      let actions = {
        nozzle: "kline_sharing",
        code: that.$store.state.codeName
      };
      that.websocketsend(JSON.stringify(actions));
      clearInterval(that.infoC4WebSocketTimer); // 推送计时器
      that.infoC4WebSocketTimer = setInterval(() => {
        that.websocketsend(JSON.stringify(actions));
      }, 2000);
    },
    websocketonerror(){//连接建立失败重连
      setTimeout(() => {
        this.websock.close() //离开路由之后断开websocket连接
        this.initWebSocket();
        if (navigator.onLine) {
          // console.log("重连 有.... 网");
          this.websocketBoolean = true;
        }else{
          // console.log("重连 没 网");
          this.$message.error("请查看您的网络连接");
          this.websocketBoolean = false;
        };
      }, 20000);
    },
    websocketsend(Data){//数据发送
      this.websock.send(Data);
    },
    websocketonmessage(e){ //数据接收
      let that = this;
      if (e.data.slice(0, 4) != "连接成功") {
        const redata = JSON.parse(e.data);
        // console.log(redata);
        sessionStorage.setItem("drawNumPoint", redata.data.data.price); // 小数位
        that.infoFiveSellPrice = redata.data.five_gear.ask[4].price;// 五档卖出
        that.infoFiveSellNum = redata.data.five_gear.ask[4].number;// 五档卖出
        that.infoFiveBuyPrice = redata.data.five_gear.bid[0].price;// 五档买入
        that.infoFiveBuyNum = redata.data.five_gear.bid[0].number;// 五档买入
        that.newInfoFace = redata.data.data; // 最新涨跌中间信息
        that.timePriceHands = redata.data.tick; // 时间价格现手
      }
    },
    websocketclose(e){  //关闭
      // console.log('断开连接',e);
      let that = this;
      setTimeout(() => {
        if (that.websocketBoolean) {
          that.websock.close() //离开路由之后断开websocket连接
          that.initWebSocket();
        }
        if (navigator.onLine) {
          // console.log("断开后有网");
          that.websocketBoolean = true;
        }else{
          // console.log("断开后没网");
          that.websocketBoolean = false;
        };
      }, 2000);
    },

    handleCloseTfC2Menu(e) {
      // 点击空白部分关闭右键
      this.isShowTfC2Menu = false;
    },
    infoC2RightMenuFun() {
      // 右键弹出菜单
      this.isShowTfC2Menu = true;
      this.activeLeftMenu = event.pageX; //给left赋值
      this.activeTopMenu = event.pageY; //给top赋值
      // let totalWidth = this.$refs.klineContainer.getBoundingClientRect().right; //总宽度
      // if (totalWidth - event.pageX < 150) {
      //   //边缘检测
      //   this.activeLeftMenu = event.pageX - 150; //给left赋值
      //   this.activeTopMenu = event.pageY; //给top赋值
      // } else {
      //   this.activeLeftMenu = event.pageX + 1; //给left赋值
      //   this.activeTopMenu = event.pageY + 1; //给top赋值
      // }
    },
    infoC2MenuChooseFun(index) { // 右键K线指标
      this.isShowTfC2Menu = false;
      for (let i = 0; i < this.menuLists.length; i++) {
        if (this.menuLists[i].id == index) {
          this.menuLists[i].isClicked = true;
        } else {
          this.menuLists[i].isClicked = false;
        }
      }
    },
  },
  beforeRouteLeave(to, from, next) {
    //切换路由 离开页面清除计时器
    clearInterval(this.infoC4GetTimer);
    this.infoC4GetTimerBoole = false; // 计时器清除
    next();
  },
  beforeDestroy() {
    //在开始销毁实例时调用。此时实例仍然有功能。
    //清除定时器
    clearInterval(this.infoC4GetTimer);
    // console.log(window._kline.default.instance.timer)
    window.clearTimeout(window._kline.default.instance.timer);
    this.infoC4GetTimerBoole = false;

    clearInterval(this.infoC4WebSocketTimer); // 推送计时器
    this.websocketBoolean = false;
    this.websock.close() //离开路由之后断开websocket连接
  }
};
</script>
<style scoped>
.tf_right_menu {
  position: fixed!important;
  z-index: 2500!important;
  width: 150px!important;
  height: 200px!important;
  background: white;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.4);
}

.tf_right_menu_ul {
  width: 100%;
  height: 200px!important;
  overflow-y: auto;
  padding: 0!important;
}

/* 滚动条整体部分 */
.tf_right_menu_ul::-webkit-scrollbar {
  width: 6px;
  height: 0;
  background-color: rgba(230, 230, 232, 1);
  text-align: center;
}

/* 外层轨道 */
.tf_right_menu_ul::-webkit-scrollbar-track {
  background-color: rgba(230, 230, 232, 1);
  border-radius: 4px;
  width: 8px;
  padding: 0 4px;
}

/* 滚动条滑块 */
.tf_right_menu_ul::-webkit-scrollbar-thumb {
  border-radius: 4px;
  background: rgba(153, 153, 153, 1);
  width: 8px;
}

.tf_right_menu_li {
  width: 100%;
  height: 25px!important;
  font-size: 14px;
  line-height: 25px;
  color: #333;
}

.tf_right_menu_li:hover {
  background-color: rgba(232, 232, 232, 1);
}
.tf_right_menu_li_active {
  background-color: rgba(232, 232, 232, 1);
}
.tf_right_menu_li a {
  display: inline-block;
  width: 100%;
  height: 25px;
  font-size: 14px;
  line-height: 25px;
  color: #333;
  padding: 0 0 0 15px!important;
}

.infofacechild4 {
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
  background: gainsboro;
  vertical-align: top;
}
a.kxiana {
  text-decoration: none;
}
a.kxiana:hover {
  text-decoration: underline !important;
}
#chart_canvasGroup:after {
  display: block;
  width: 100%;
  height: 100%;
  content: "";
  /* background: url("/statics/img/common/bg_klie.png") 50% 25% no-repeat;
  background-size: auto 22%; */
  position: absolute;
  z-index: 1;
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