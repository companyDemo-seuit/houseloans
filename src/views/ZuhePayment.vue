<template>

  <div class="outerLayerDiv">
    <div style="margin:0 auto;width:220px;">
    <button-tab v-model="selectedItem">
        <button-tab-item @on-item-click="itemChange">等额本息</button-tab-item>
        <button-tab-item @on-item-click="itemChange">等额本金</button-tab-item>
    </button-tab>

    <p class="disctext">{{lineOne}}</p>
    <RingChart :ringList="ringList" :houseTotalPrice="houseTotalPrice" :monthPay="monthPay"></RingChart>

    <ul>
      <li class="houseMoney">
        <span></span>
        <span>首月月供</span>
        <span>:</span>
        <span>{{this.monthPay}}</span>
        <span>元/月</span>
      </li>
      <li class="houseMoney" v-if="noEq">
        <span></span>
        <span>前 {{lessYear}} 月</span>
        <span>:</span>
        <span>{{this.monthPay}}</span>
        <span>元/月</span>
      </li>
      <li class="houseMoney" v-if="noEq">
        <span></span>
        <span>后 {{moreYear}} 月</span>
        <span>:</span>
        <span>{{afterPay}}</span>
        <span>元/月</span>
      </li>
       <li v-show="this.selectedItem==1" class="houseMoney">
        <span></span>
        <span>每月递减</span>
        <span>:</span>
        <span>{{this.monthDecMoney}}</span>
        <span>元/月</span>
      </li>
      <li class="houseMoney" v-if="noSame">
        <span></span>
        <span>第 {{lastMonth}} 月</span>
        <span>:</span>
        <span>{{lastMonthpay}}</span>
        <span>元/月</span>
      </li>
      <li class="houseMoney" v-if="noSame">
        <span></span>
        <span>每月递减</span>
        <span>:</span>
        <span>{{lastLess}}</span>
        <span>元/月</span>
      </li>
    </ul>
    <p class="disctext" style="text-align:center">以上结果仅供参考</p>
    </div>
  </div>

</template>
<script>
import { ButtonTab, ButtonTabItem, Divider } from "vux";
import RingChart from "../components/RingChart"
import LoanCaculate from "../components/LoanCaculate"
export default {
  components: {
    ButtonTab,
    ButtonTabItem,
    Divider,
    RingChart
  },
  mounted(){
    // this.totalPrice= this.$route.query.sloanMoney
    this.sloanMoney= this.$route.query.sloanMoney*10000;
    this.gloanMoney= this.$route.query.gloanMoney*10000;
    this.totalPrice= this.sloanMoney + this.gloanMoney;//贷款总价
    this.smonths = this.$route.query.smonths;
    this.gmonths = this.$route.query.gmonths;
    this.srate = this.$route.query.srate;
    this.grate = this.$route.query.grate;
    this.caculateAcpi(true)
  },
  methods: {
    itemChange(){
      if(this.selectedItem==0){
        this.caculateAcpi(true);
        this.noSame = false;
      }else{
        this.caculateAcpi(false);
        this.noEq = false;
      }

    },
    caculateAcpi(isAcpi) {
      let totalMonthpay = 0;
      let smonthPay = 0;
      let totalPay=0;
      let gmonthPay = 0;
      if(isAcpi){
         smonthPay =  LoanCaculate.Acpi(this.srate,this.sloanMoney,this.smonths);//商贷月付
         gmonthPay =  LoanCaculate.Acpi(this.grate,this.gloanMoney,this.gmonths);//商贷月付
         totalMonthpay = smonthPay + gmonthPay;
         totalPay = smonthPay*this.smonths + gmonthPay*this.gmonths;
         if(this.smonths != this.gmonths){
             this.noEq = true;
             this.lessYear = (Math.min(this.smonths,this.gmonths));
             this.moreYear = (Math.abs(this.smonths-this.gmonths));
             if(this.smonths > this.gmonths){
                 this.afterPay = smonthPay.toFixed(0);
             }else{
                 this.afterPay = gmonthPay.toFixed(0);
             }
         };
         this.lineOne = "每月还款额固定，所还总利息较多，适合收入稳定者。"
      }else{
         for(let month=0;month<this.smonths;month++){
          smonthPay =  LoanCaculate.AverageCapital(this.srate,this.sloanMoney,this.smonths,month)//商业贷款月付
          totalPay += smonthPay;//商贷共付
         }
         for(let month=0;month<this.gmonths;month++){
          gmonthPay =  LoanCaculate.AverageCapital(this.grate,this.gloanMoney,this.gmonths,month)//公积金贷款月付
          totalPay += gmonthPay;//公积金共付
         }
         if(this.smonths != this.gmonths){
             this.noSame = true;
             this.lastMonth = Math.min(this.smonths,this.gmonths)+1;

              if(this.smonths > this.gmonths){
                  this.lastMonthpay = parseInt(LoanCaculate.AverageCapital(this.srate,this.sloanMoney,this.smonths,this.lastMonth));
                  this.lastLess = (LoanCaculate.AverageCapital(this.srate,this.sloanMoney,this.smonths,0) - LoanCaculate.AverageCapital(this.srate,this.sloanMoney,this.smonths,1)).toFixed(2);
             }else{
                  this.lastMonthpay = parseInt(LoanCaculate.AverageCapital(this.grate,this.gloanMoney,this.gmonths,this.lastMonth));
                  this.lastLess = (LoanCaculate.AverageCapital(this.grate,this.gloanMoney,this.gmonths,0) - LoanCaculate.AverageCapital(this.grate,this.gloanMoney,this.gmonths,1)).toFixed(2);

             }
         }
         totalMonthpay =  LoanCaculate.AverageCapital(this.srate,this.sloanMoney,this.smonths,0) + LoanCaculate.AverageCapital(this.grate,this.gloanMoney,this.gmonths,0);
         let nextmonthPay =  LoanCaculate.AverageCapital(this.srate,this.sloanMoney,this.smonths,1) + LoanCaculate.AverageCapital(this.grate,this.gloanMoney,this.gmonths,1);
         this.monthDecMoney = (totalMonthpay-nextmonthPay).toFixed(2);
         this.lineOne = "每月还款额递减，所还总利息较低，前期还款额较大。"
      }
      //console.log("月供",Math.ceil(monthPay));
      this.monthPay = Math.ceil(totalMonthpay);

      //console.log("总还款",Math.ceil(totalPay));
      let totalInterest = totalPay - this.sloanMoney - this.gloanMoney;
      //console.log("总利息",Math.ceil(totalInterest));
      this.houseTotalPrice={name:"还款总价",value:(totalPay/10000).toFixed(2)}//还款总价,cuowu
      this.ringList=[
        // { name: "首付金额", value: (~~this.payMoney).toFixed(2), color: "#6EBFFF" },
        { name: "贷款总价", value: (this.totalPrice/10000).toFixed(4),unit: "万元", color: "#FFDA7C" },
        { name: "支付利息", value: (totalInterest/10000).toFixed(4),unit: "万元", color: "#FF70A0" }
      ]
    }
  },
  data() {
    return {
      selectedItem:0,
      lineOne: "每月还款额固定，所还总利息较多，适合收入稳定者。",
      ringList:[],
      houseTotalPrice:"",
      sloanMoney:0,
      gloanMoney:0,
      payMoney:0,
      smonths:0,
      gmonths:0,
      monthPay:0,
      srate:0,
      grate:0,
      monthDecMoney:0,
      lessYear:0,
      moreYear:0,
      afterPay:0,
      noEq:false,
      noSame:false,
      lastMonth:0,
      lastMonthpay:0,
      lastLess:0
    }
  }
}
</script>
<style>

.outerLayerDiv .vux-button-group {
    -webkit-touch-callout: none;
    display: box;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    background: #4270d0 !important;
    padding: 5%;
width: 90%;
position: absolute;
left: -0%;
}
.vux-button-group > a{
      color: #fff!important;
          background: #4270d0 !important;
}
.vux-button-group > a.vux-button-group-current {
    background: #fff !important;
          color: #4270d0!important;
}

.vux-button-group > a.vux-button-tab-item-first:after {
    content: " ";
    position: absolute;
    left: 0;
    top: 0;
    width: 200%;
    height: 1px;
        /* color: #fff!important;
        background: #4270d0!important;*/
    border: 1px solid #fff!important;
    height: 200%;
    -webkit-transform-origin: left top;
    transform-origin: left top;
    -webkit-transform: scale(0.5);
    transform: scale(0.5);
    border-top-left-radius: 32px;
    border-bottom-left-radius: 32px;
}

.vux-button-group > a.vux-button-tab-item-middle:after {
    content: " ";
    position: absolute;
    left: 0;
    top: 0;
    width: 200%;
    height: 1px;
        /* color: #fff!important;
        background: #4270d0!important;*/
    border-right: 1px solid #fff!important;
    border-top: 1px solid #fff!important;
    border-bottom: 1px solid #fff!important;
    border-left: none;
    height: 200%;
    -webkit-transform-origin: left top;
    transform-origin: left top;
    -webkit-transform: scale(0.5);
    transform: scale(0.5);
}
.vux-button-group > a.vux-button-tab-item-last:after {
    content: " ";
    position: absolute;
    left: 0;
    top: 0;
    width: 200%;
    height: 1px;
        /* color: #fff!important;*/
    border-right: 1px solid #fff!important;
    border-top: 1px solid #fff!important;
    border-bottom: 1px solid #fff!important;
    border-left: none;
    height: 200%;
    -webkit-transform-origin: left top;
    transform-origin: left top;
    -webkit-transform: scale(0.5);
    transform: scale(0.5);
    border-top-right-radius: 32px;
    border-bottom-right-radius: 32px;
}

</style>
