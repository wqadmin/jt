<template>
  <div>
    <div class="nav_top_wrap">
      <div class="nav_top">
        <div class="nav_top_sys" id="header_id" @dblclick="dbclickWinFun" :class="{nav_top_sys_black: $store.state.themeStyle ==2}">
          <div class="nav_top_sys_logo" :title="$t('nav.title')">
            <img src="../assets/img/logo.png" alt="logo">
            <span>{{$t('nav.logo')}}</span>
          </div>
          <div class="nav_top_sys_list" @click="navTopShowFun(1)" v-clickoutside="handleCloseSys">
            <span :title="$t('nav.sys')">{{$t('nav.sys')}}</span>
            <ul v-show="isNavTopSys">
              <li @click="navTopSysFun" :title="$t('nav.sysExit')">{{$t('nav.sysExit')}}</li>
            </ul>
          </div>
          <div class="nav_top_sys_list" @click="navTopShowFun(2)" v-clickoutside="handleCloseStyle">
            <span :title="$t('nav.style')">{{$t('nav.style')}}</span>
            <ul v-show="isNavTopStyle">
              <li @click="navTopStyleFun(1)">{{$t('nav.slyleGray')}}</li>
              <li @click="navTopStyleFun(2)">{{$t('nav.slyleBlack')}}</li>
            </ul>
          </div>
          <div class="nav_top_sys_list" @click="navTopShowFun(3)" v-clickoutside="handleCloseHelp">
            <span :title="$t('nav.help')">{{$t('nav.help')}}</span>
            <ul v-show="isNavTopHelp">
              <li @click="navTopHelpFun(1)">{{$t('nav.helpDisclaimer')}}</li>
              <li @click="navTopHelpFun(2)">{{$t('nav.helpAbout')}}</li>
            </ul>
          </div>
          <span v-show="$store.state.noLoginOutTran" @click="startLoginF12Fun" :title="$t('nav.out')">{{$t('nav.out')}}</span>
          <span v-show="$store.state.isLoginedOutTran" @click="loginedF12Fun" :title="$t('nav.out')">{{$t('nav.out')}}</span>
          <!-- <div class="nav_top_sys_list" @click="navTopShowFun(4)" v-clickoutside="handleCloseLang">
            <span :title="$t('nav.lang')">{{$t('nav.lang')}}</span>
            <ul v-show="isNavTopLang">
              <li @click="navTopLangFun(1)">简体中文</li>
              <li @click="navTopLangFun(2)">繁体中文</li>
              <li @click="navTopLangFun(3)">English</li>
            </ul>
          </div> -->
          <div class="nav_top_sys_btns">
            <img src="../assets/img/windowSmall.png" @click.stop="navTopWindowFun(1)" :title="$t('nav.min')">
            <img src="../assets/img/windowBig.png" @click.stop="navTopWindowFun(2)" v-show="navTopWindow==true" :title="$t('nav.max')">
            <img src="../assets/img/windowMid.png" @click.stop="navTopWindowFun(3)" v-show="navTopWindow==false" :title="$t('nav.restore')">
            <img src="../assets/img/windowClose.png" @click.stop="navTopWindowFun(4)" :title="$t('nav.close')">
          </div>
        </div>
        <div class="nav_top_btm_wrap">
          <ul class="nav_top_btm">
            <li
              class="nav_top_btm_li"
              @click="navTopBtnFun(topBtn.id)"
              v-for="topBtn in topBtnList1"
              :key="topBtn.id"
              :title="topBtn.content"
            >
              <img :src="topBtn.t_src" alt>
            </li>
            <li
              class="nav_top_btm_li"
              @click="topClick(topBtn.time,topBtn.type)"
              v-for="topBtn in topBtnList2"
              :key="topBtn.id"
              :title="topBtn.content"
            >
              <img :src="topBtn.t_src" alt>
            </li>
            <li
              class="nav_top_btm_li"
              @click="scale('e',topBtn.type)"
              v-for="topBtn in topBtnList3"
              :key="topBtn.id"
              :title="topBtn.content"
            >
              <img :src="topBtn.t_src" alt>
            </li>
            <li
              class="nav_top_btm_li"
              @click="clickline(topBtn.type)"
              v-for="topBtn in topBtnList5"
              :key="topBtn.id"
              :title="topBtn.content"
            >
              <img :src="topBtn.t_src" alt>
            </li>
            <!-- 画线工具 -->
            <!-- <li
              class="nav_top_btm_li"
              @click="showTool()"
              :key="lineBox.id"
              :title="lineBox.content"
              :class="lineBox.ishave ? 'selected' : '' "
            >
              <img :src="lineBox.t_src" alt>
            </li> -->
            <li
              class="nav_top_btm_li"
              v-for="topBtn in topBtnList6"
              @click="leftRight(topBtn.type)"
              :key="topBtn.id"
              :title="topBtn.content"
            >
              <img :src="topBtn.t_src" alt>
            </li>
            <li
              class="nav_top_btm_li"
              @click="navTopBtnFun(topBtn.id)"
              v-for="topBtn in topBtnList4"
              :key="topBtn.id"
              :title="topBtn.content"
              :id="topBtn.idName"
            >
              <img :src="topBtn.t_src" alt>
            </li>
          </ul>
          <div class="nav_top_btm_wrap_l"></div>
          <!-- <img src="../assets/img/topAlert.png" alt title="预警设置"> -->
        </div>
      </div>
    </div>
    <!-- <Tranalertchangepass :msg="tranChangePass" v-if="tranChangePass.tranAlertChangePass"></Tranalertchangepass> -->
  </div>
</template>

<script>
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
  name: "Navtop",
  components: {
    // Tranalertchangepass
  },
  data() {
    return {
      isNavTopSys: false, //系统显示初始值
      isNavTopStyle: false, //风格显示初始值
      isNavTopHelp: false, //帮助显示初始值
      isNavTopLang: false, // 语言切换
      // lang: "zh-CN",
      navTopWindow: true, // 显示窗口
      topBtnList1: [ //自选报价分时图
        {
          id: "1",
          content: "自选合约",
          isChoose: true, //选择里面的选择中
          isClicked: true, //点击头部按钮选中
          t_src: require("../assets/img/01.png")
        },
        {
          id: "2",
          content: "报价表",
          isChoose: true, //选择里面的选择中
          isClicked: false, //点击头部按钮选中
          t_src: require("../assets/img/02.png")
        },
        {
          id: "3",
          content: "分时图",
          isChoose: true, //选择里面的选择中
          isClicked: false, //点击头部按钮选中
          t_src: require("../assets/img/03.png")
        },
        {
          id: "4",
          content: "K线",
          isChoose: true, //选择里面的选择中
          isClicked: false, //点击头部按钮选中
          t_src: require("../assets/img/04.png")
        },
        {
          id: "5",
          content: "刷新",
          isChoose: true, //选择里面的选择中
          isClicked: false, //点击头部按钮选中
          t_src: require("../assets/img/05.png")
        }
      ],
      topBtnList2: [ //切换时间
        {
          id: "6",
          content: "1分钟",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/06.png"),
          time: "1m",
          type: "001"
        },
        // {
        //   id: "7",
        //   content: "3分钟",
        //   isChoose: true,
        //   isClicked: false,
        //   t_src: require("../assets/img/07.png"),
        //   time: "1m",
        //   type: "001"
        // },
        {
          id: "8",
          content: "5分钟",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/08.png"),
          time: "5m",
          type: "005"
        },
        {
          id: "9",
          content: "15分钟",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/09.png"),
          time: "15m",
          type: "015"
        },
        {
          id: "10",
          content: "30分钟",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/10.png"),
          time: "30m",
          type: "030"
        },
        {
          id: "11",
          content: "1小时",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/11.png"),
          time: "1h",
          type: "060"
        },
        {
          id: "12",
          content: "1天",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/12.png"),
          time: "1d",
          type: "100"
        },
        {
          id: "13",
          content: "1周",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/13.png"),
          time: "1w",
          type: "200"
        },
        // {
        //   id: "14",
        //   content: "1月",
        //   isChoose: true,
        //   isClicked: false,
        //   t_src: require("../assets/img/14.png"),
        //   time: "1mon",
        //   type: "300"
        // }
      ],
      topBtnList3: [ //放大缩小
        {
          id: "15",
          content: "放大",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/15.png"),
          type: "big"
        },
        {
          id: "16",
          content: "缩小",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/16.png"),
          type: "small"
        }
      ],
      topBtnList4: [
        // {
        //   id: "28",
        //   content: "标注",
        //   isChoose: true,
        //   isClicked: false,
        //   t_src: require("../assets/img/28.png"),
        //   idName:""
        // },
        // {
        //   id: "29",
        //   content: "上箭头",
        //   isChoose: true,
        //   isClicked: false,
        //   t_src: require("../assets/img/29.png"),
        //   idName:""
        // },
        // {
        //   id: "30",
        //   content: "下箭头",
        //   isChoose: true,
        //   isClicked: false,
        //   t_src: require("../assets/img/30.png"),
        //   idName:""
        // },
        {
          id: "32",
          content: "全删",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/32.png"),
          idName:"clearLine"
        }
      ],
      topBtnList5: [
        {
          id: "19",
          content: "画线下单",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/19.png"),
          type: 13 // type是原Kline.js里面的参数
        },
        {
          id: "20",
          content: "趋势线",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/20.png"),
          type: 6
        },
        // {
        //   id: "21",
        //   content: "射线",
        //   isChoose: true,
        //   isClicked: false,
        //   t_src: require("../assets/img/21.png"),
        //   type: 8
        // },
        {
          id: "22",
          content: "水平线",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/22.png"),
          type: 9
        },
        {
          id: "23",
          content: "竖线",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/23.png"),
          type: 12
        },
        // {
        //   id: "24",
        //   content: "线段",
        //   isChoose: true,
        //   isClicked: false,
        //   t_src: require("../assets/img/24.png"),
        //   type: 5
        // },
        {
          id: "25",
          content: "箭头", //箭头
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/25.png"),
          type: 7
        },
        {
          id: "26",
          content: "通道线",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/26.png"),
          type: 14
        },
        {
          id: "27",
          content: "黄金率",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/27.png"),
          type: 3
        }
      ],
      topBtnList6: [
        {
          id: "17",
          content: "左移动",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/17.png"),
          idName: "moveLeft",
          type: "left"
        },
        {
          id: "18",
          content: "右移动",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/18.png"),
          idName: "moveRight",
          type: "right"
        }
      ],
      lineBox:{
          id: "31",
          content: "画线工具",
          isChoose: true,
          isClicked: false,
          t_src: require("../assets/img/31.png"),
          ishave:false
        },
      time: "", //默认时间点
    };
  },
  mounted: function() {
    // console.log(this.$store.state.isLoginedOutTran);
    if (this.$store.state.isLoginedOutTran == true) {
      // 已登录
      this.isShowStartLoginOut = false;
      this.isShowOutTranFace = true;
    } else {
      // 未登录
      this.isShowStartLoginOut = true;
      this.isShowOutTranFace = false;
    }
    // window.localStorage.setItem("isOutFaceOut", true); // 默认 是 不弹出状态

    var $win = window;
    var $nav = document.getElementById('header_id');
    var dragging = false; //拖曳
    var mouse_x, mouse_y;
    var win_x, win_y;
    //鼠标拖动
    document.getElementById('header_id').onmousedown = function (e) {
        if (window.localStorage.getItem('set_dragging') == '0') {
            e = e.originalEvent || e;
            var isbg = $(e.target).closest('.navbar-nav, #form-user').length < 1;
            if (!isbg) return;
            dragging = true;
            mouse_x = e.screenX;
            mouse_y = e.screenY;

            win_x = win.x;
            win_y = win.y
        } else {
            return false;
        }
    };

    //鼠标移动时
    window.onmousemove = function (e) {
        if (!dragging) return;
        win.x = win_x + (e.screenX - mouse_x);
        win.y = win_y + (e.screenY - mouse_y)
    };

    //鼠标放开
    window.onmouseup = function () {
        dragging = false
    };
  },
  methods: {
    navTopLangFun(index) { //语言切换
      if (index == 1) {
        // this.$i18n.locale = 'zh-CN';//简体中文
        this.$i18n.locale = "cn";
        localStorage.setItem('language', "cn");
        console.log(this.$i18n.locale);
      } else if (index == 2) {
        // this.$i18n.locale = 'zh-TW';//繁体中文
        this.$i18n.locale = "tw";
        localStorage.setItem('language', "tw");
      } else if (index == 3) {
        // this.$i18n.locale = 'en-US';//英文
        // localStorage.setItem('language', "en-US");
        this.$i18n.locale = "en";
        localStorage.setItem('language', "en");
      }
    },
    navTopWindowFun(index) {
      let that = this;
      if (index == 1) { // 最小化
        win.minimize();
        win.leaveFullscreen()
      } else if (index == 2) { // 最大化
        that.navTopWindow = false;
        maximize_window();
        // win.enterFullscreen();
      } else if (index == 3) { //向下还原
        that.navTopWindow = true;
        restore_window();
      } else if (index == 4) { //关闭
        that.$store.commit("isWinShowFun");
      }
    },
    dbclickWinFun() { //双击
      if (this.navTopWindow) {
        this.navTopWindowFun(2);
      } else {
        this.navTopWindowFun(3);
      }
    },
    //画线工具的展示与隐藏
    showTool(){
      if (this.$route.path == "/wrap/infoFace/infoFaceChild4") {
        //在K线页面
        // window.Kline.instance.chartMgr.setRunningMode(type);
        if(this.lineBox.ishave == false){
          this.lineBox.ishave = true;
          window._control.Control.switchTools('on');
        }else{
          this.lineBox.ishave = false;
          window._control.Control.switchTools('off');
        }
      }
    },
    //选择画线工具
    clickline(type) {
      if (this.$route.path == "/wrap/infoFace/infoFaceChild4") {
        if (type != 13) {
          window.Kline.instance.chartMgr.setRunningMode(type); // 其他的划线
        }
        if (type == 13) { //画线下单
          if (this.$store.state.tokenStr) {
            //在K线页面且已登录
            this.$store.commit("isDrawOrderFun");
            this.$nextTick(()=>{
              this.$parent.$refs.drawOrder.getOrderOneInfoFun(this.$store.state.codeName); // 获取选中的合约的信息
            });
            window.sessionStorage.setItem("lineType", type); // 画线下单时用
          } else {
            this.$message.warning("请先登录交易账号");
          }
        }
      } else {
        this.$message.warning("请先切换至K线");
      }
    },
    //选择时间段
    topClick(time, type) {
      let that = this;
      // window.sessionStorage.setItem("type", type); //001 005 015 030 060 100 200
      if (that.$route.path != "/wrap/infoFace/infoFaceChild4") {
        //不在K线页面
        this.$router.push({
          path: "/wrap/infoFace/infoFaceChild4"
        });
        // 调用infoFace里面的函数改变K线按钮
        that.$parent.$refs.route.infoLeftBtnsActiveFun(4);
        that.$nextTick(()=>{
          // window.clearTimeout(window._kline.default.instance.timer);
          window._control.Control.switchPeriod(time);
        });
      } else {
        //在K线页面
        window._control.Control.switchPeriod(time);
        // if (that.time != time) {
        //   that.time = time;
        //   // window.clearTimeout(window._kline.default.instance.timer);
        //   window._control.Control.switchPeriod(time);
        // }
      }
    },
    //放大缩小
    scale(e,type) {
      if (this.$route.path == "/wrap/infoFace/infoFaceChild4") {
        if (type == "big") {
          //放大
          window.Control.mouseWheel(e,1);
        } else if (type == "small") {
          //缩小
          window.Control.mouseWheel(e,-1);
        }
      }
    },
    //左移 右移
    leftRight(type) {
      if (this.$route.path == "/wrap/infoFace/infoFaceChild4") {
        if (type == "left") {
          window.ChartManager.instance
            .getTimeline("frame0.k0")
            .move(200, "click");
        } else {
          window.ChartManager.instance
            .getTimeline("frame0.k0")
            .move(-200, "click");
        }
      }
    },
    handleCloseSys() {
      //点击空白关闭系统
      this.isNavTopSys = false;
    },
    handleCloseStyle() {
      //点击空白关闭风格
      this.isNavTopStyle = false;
    },
    handleCloseHelp() {
      //点击空白关闭帮助
      this.isNavTopHelp = false;
    },
    handleCloseLang() {
      //点击空白关闭语言
      this.isNavTopLang = false;
    },
    navTopShowFun(index) { // 系统 风格 帮助 按钮
      if (index == 1) {
        if (this.isNavTopSys) {
          this.isNavTopSys = false; // 系统显示
        } else {
          this.isNavTopSys = true; // 系统显示
          // this.isNavTopStyle = false;
          // this.isNavTopHelp = false;
        }
      } else if (index == 2) {
        if (this.isNavTopStyle) {
          this.isNavTopStyle = false; //风格显示
        } else {
          this.isNavTopStyle = true; //风格显示
          // this.isNavTopSys = false;
          // this.isNavTopHelp = false;
        }
      } else if (index == 3) {
        if (this.isNavTopHelp) {
          this.isNavTopHelp = false; //帮助显示
        } else {
          this.isNavTopHelp = true; //帮助显示
          // this.isNavTopSys = false;
          // this.isNavTopStyle = false;
        }
      } else if (index == 4) {
        if (this.isNavTopLang) {
          this.isNavTopLang = false; //语言显示
        } else {
          this.isNavTopLang = true; //语言显示
          // this.isNavTopSys = false;
          // this.isNavTopStyle = false;
        }
      }
    },
    navTopSysFun() { // 退出登录
      // console.log("退出登录");
      this.$store.commit("isWinShowFun");
    },
    navTopStyleFun(index) { // 风格里面点击事件
      if (index == 1) {
        // console.log("灰色风格");
        this.$store.commit("themeStyleFun", 1);
        window.localStorage.setItem("theme", 1); // 保存主题
      } else if (index == 2) {
        // console.log("黑色风格");
        this.$store.commit("themeStyleFun", 2);
        window.localStorage.setItem("theme", 2); // 保存主题
      }
    },
    navTopHelpFun(index) { // 帮助里面点击事件
      if (index == 1) {
        // console.log("免责声明");
        this.$store.commit("isloginDisclaimerFun");
      } else if (index == 2) {
        // console.log("关于我们");
        this.$store.commit("isAboutUsFun");
      }
    },
    startLoginF12Fun() {
      // 未登录
      this.$store.commit("loginOutTranFun");
    },
    loginedF12Fun() {
      // F12按钮
      if (this.$store.state.isShowAlertTface) {
        // 已弹出
        if (this.$store.state.isShowTf) {
          this.$store.commit("isShowTfFun", false); // // 已登录按F12外盘显隐控制
        } else {
          this.$store.commit("isShowTfFun", true); // // 已登录按F12外盘显隐控制
        }
      } else {
        // 未弹出
        if (this.$store.state.isShowTf) {
          this.$store.commit("isShowTfFun", false); // // 已登录按F12外盘显隐控制
        } else {
          this.$store.commit("isShowTfFun", true); // // 已登录按F12外盘显隐控制
        }
        if (this.$store.state.infoFaceHeight) {
          this.$store.commit("infoFaceHeightFun", false); // 已登录点击F12按钮高低控制
        } else {
          this.$store.commit("infoFaceHeightFun", true); // 已登录点击F12按钮高低控制
        }
        if (this.$route.path == "/wrap/infoFace/infoFaceChild3") {
          this.$nextTick(() => {
            window.myChart.resize();
          })
        }
        if (this.$route.path == "/wrap/infoFace/infoFaceChild4") {
          this.$parent.$refs.route.$refs.infoRoute.infoChangeKlineWHFun(); // 调用改变K线宽高
        }
      }
    },
    navTopBtnFun(index) {
      //点击头部按钮
      let that = this;
      if (index == 1) { // 自选
        that.$router.push({
          path: "/wrap/infoFace/infoFaceChild1"
        });
        // 调用infoFace里面的函数改变自选按钮
        that.$parent.$refs.route.infoLeftBtnsActiveFun(1);
      }
      if (index == 2) { // 报价
        that.$router.push({
          path: "/wrap/infoFace/infoFaceChild2"
        });
        // 调用infoFace里面的函数改变报价按钮
        that.$parent.$refs.route.infoLeftBtnsActiveFun(2);
      }
      if (index == 3) { // 分时
        that.$router.push({
          path: "/wrap/infoFace/infoFaceChild3"
        });
        // 调用infoFace里面的函数改变分时按钮
        that.$parent.$refs.route.infoLeftBtnsActiveFun(3);
      }
      if (index == 4) { // K线
        that.$router.push({
          path: "/wrap/infoFace/infoFaceChild4"
        });
        // 调用infoFace里面的函数改变K线按钮
        that.$parent.$refs.route.infoLeftBtnsActiveFun(4);
      }
      if (index == 5) { // 刷新
        window.location.reload();
      }
    },
  },
  directives: { clickoutside }, //自定义指令点击空白右键消失
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* @import "../assets/css/kline.css"; */
.nav_top {
  position: relative;
  z-index: 1000;
  top: 0;
  width: 100%;
  height: 60px;
  background: rgb(235, 236, 239);
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.nav_top_sys {
  width: 100%;
  height: 30px;
  background-color: rgba(68, 68, 68, 1);
  min-width: 650px;
}
/* 黑色风格 */
.nav_top_sys_black {
  background-color: rgba(51, 51, 51, 1);
}
.nav_top_sys_logo {
  display: inline-block;
  margin-right: 20px;
  padding-left: 5px;
}
.nav_top_sys_logo>img {
  width: 20px;
  height: 20px;
  vertical-align: middle;
}
.nav_top_sys_logo>span {
  color: rgba(255, 255, 255, 1);
  font-size: 14px;
  line-height: 30px;
  vertical-align: middle;
  -webkit-app-region:no-drag;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
.nav_top_sys>span {
  display: inline-block;
  height: 30px;
  color: rgba(255, 255, 255, 1);
  text-align: center;
  font-size: 14px;
  line-height: 30px;
  padding: 0 10px;
  cursor: pointer;
  vertical-align: middle;
}
.nav_top_sys>span:hover {
  background: #a5a5a5;
}

.nav_top_sys_list {
  position: relative;
  display: inline-block;
  height: 30px;
  text-align: center;
  cursor: pointer;
}
.nav_top_sys_list>span {
  display: inline-block;
  color: rgba(255, 255, 255, 1);
  font-size: 14px;
  line-height: 30px;
  height: 30px;
  vertical-align: middle;
  padding: 0 10px;
}
.nav_top_sys_list>span:hover {
  background: #a5a5a5;
}
.nav_top_sys_list>ul {
  position: absolute;
  top: 30px;
}
.nav_top_sys_list>ul>li {
  width: 100px;
  height: 30px;
  font-size: 14px;
  line-height: 30px;
  color: rgba(96, 96, 96, 0.91);
  font-size: 14px;
  background-color: rgba(255, 255, 255, 1);
  text-align: left;
  padding: 0 10px;
}
.nav_top_sys_list>ul>li:hover {
  background-color: rgba(202, 202, 202, 1);
}

.nav_top_sys_btns {
  float: right;
  height: 30px;
  -webkit-app-region:no-drag;
}

.nav_top_sys_btns> img {
  width: 30px;
  height: 30px;
  padding: 8px;
  -webkit-app-region:no-drag;
}
.nav_top_sys_btns> img:hover {
  background: #a5a5a5;
}

.nav_top_btm {
  min-width: 960px;
  font-size: 0;
}
.nav_top_btm_li {
  display: inline-block;
  width: 28px;
  height: 28px;
  text-align: center;
  border: 1px solid rgba(0, 0, 0, 0);
}
.nav_top_btm_li:hover {
  border: 1px solid #3399ff;
  background: #c2e0ff;
}
.nav_top_btm_li > img {
  width: 18px;
  height: 18px;
  margin-top: 4px;
}
.nav_top_btm_wrap {
  display: flex;
  display: -webkit-flex;
  flex-direction: row;
  -webkit-flex-direction: row;
  justify-content: space-between;
  -webkit-justify-content: space-between;
  align-items: center;
  -webkit-align-items: center;
  cursor: default;
  width: 100%;
  height: 30px;
  background-color: rgba(244, 244, 244, 1);
}
.nav_top_btm_wrap_l {
  width: 100%;
  height: 30px;
  position: relative;
}
.nav_top_btm_wrap > img {
  width: 18px;
  height: 18px;
  margin-right: 10px;
}
</style>
