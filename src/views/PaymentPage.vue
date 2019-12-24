<template>

  <div class="outerLayerDiv">
    <div style="margin:0 auto;width:220px;padding:20px;">
    <button-tab v-model="selectedItem">
        <button-tab-item @on-item-click="itemChange">等额本息</button-tab-item>
        <button-tab-item @on-item-click="itemChange">等额本金</button-tab-item>
    </button-tab>

    <p class="disctext">{{lineOne}}</p>
    <RingChart :ringList="ringList" :houseTotalPrice="houseTotalPrice" :monthPay="monthPay"></RingChart>

    <ul>
      <li class="houseMoney">
        <span></span>
        <span class="interestrate">利&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;率</span>
        <span>:</span>
        <span><span>{{(this.rate*100).toFixed(2)}}</span><span class="sign">%</span></span>
        <span></span>
      </li>
      <li class="houseMoney">
        <span></span>
        <span>{{this.selectedItem === 0 ? '每月' : '首月'}}月供</span>
        <span>:</span>
        <span>{{this.monthPay}}</span>
        <span>元/月</span>
      </li>
       <li v-show="this.selectedItem==1" class="houseMoney">
        <span></span>
        <span>每月递减</span>
        <span>:</span>
        <span>{{this.monthDecMoney}}</span>
        <span>元/月</span>
      </li>
    </ul>
    <!-- <p class="disctext" style="text-align:center">以上结果仅供参考</p> -->
    </div>

    <x-table :cell-bordered="false" :content-bordered="false" style="background-color:#fff;">
      <thead>
        <tr style="background-color: #F7F7F7;font-size:12px;">
          <th>期数</th>
          <th>月供（元）</th>
          <th>本金（元）</th>
          <th>利息（元）</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Apple</td>
          <td>{{this.monthPay}}</td>
          <td> x 1</td>
          <td> x 1</td>
        </tr>
      </tbody>
    </x-table>
  </div>
</template>
<script>
import { XTable,ButtonTab, ButtonTabItem, Divider } from "vux";
import RingChart from "../components/RingChart"
import LoanCaculate from "../components/LoanCaculate"
export default {
  components: {
    XTable,
    ButtonTab,
    ButtonTabItem,
    Divider,
    RingChart
  },
  mounted(){
    // this.totalPrice= this.$route.query.totalPrice*10000;
    this.loanMoney= this.$route.query.loanMoney*10000;
    this.payMoney = this.$route.query.payMoney;
    this.months = this.$route.query.months;
    this.rate = this.$route.query.rate;
    this.caculateAcpi(true)
  },
  methods: {
    itemChange(){
      if(this.selectedItem==0){
        this.caculateAcpi(true);
      }else{
        this.caculateAcpi(false);
      }

    },
    caculateAcpi(isAcpi) {
      let monthPay = 0;
      let totalPay=0;
      if(isAcpi){
         monthPay =  LoanCaculate.Acpi(this.rate,this.loanMoney,this.months)
         totalPay = monthPay*this.months;
         this.lineOne = "每月还款额固定，所还总利息较多，适合收入稳定者。"
      }else{
         for(let month=0;month<this.months;month++){
          monthPay =  LoanCaculate.AverageCapital(this.rate,this.loanMoney,this.months,month)
          totalPay += monthPay;
         }
         monthPay =  LoanCaculate.AverageCapital(this.rate,this.loanMoney,this.months,0)
         let nextmonthPay =  LoanCaculate.AverageCapital(this.rate,this.loanMoney,this.months,1)
         this.monthDecMoney = (monthPay-nextmonthPay).toFixed(2);
         this.lineOne = "每月还款额递减，所还总利息较低，前期还款额较大。"
      }
      //console.log("月供",Math.ceil(monthPay));
      this.monthPay = Math.ceil(monthPay);

      //console.log("总还款",Math.ceil(totalPay));
      let totalInterest = totalPay - this.loanMoney;
      //console.log("总利息",Math.ceil(totalInterest));
      // this.houseTotalPrice={name:"房款总价",value:(this.totalPrice/10000).toFixed(2)}
      this.ringList=[
        // { name: "首付金额", value: (~~this.payMoney).toFixed(2),unit: "万元", color: "#7ec3fe" },
        { name: "贷款总额", value: (this.loanMoney/10000).toFixed(4),unit: "万元", color: "#85e3c9" },
        { name: "支付利息", value: (totalInterest/10000).toFixed(4),unit: "万元", color: "#e7db20" }
      ]
    }
  },
  data() {
    return {
      selectedItem:0,
      lineOne: "每月还款额固定，所还总利息较多，适合收入稳定者。",
      ringList:[],
      houseTotalPrice:"",
      totalPrice:0,
      loanMoney:0,
      payMoney:0,
      months:0,
      monthPay:0,
      rate:0,
      monthDecMoney:0
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
