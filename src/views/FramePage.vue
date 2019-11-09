<template>
<div>
  <!-- <tab>
    <tab-item selected @on-item-click="onItemClick">商业贷</tab-item>
    <tab-item @on-item-click="onItemClick">公积金贷</tab-item>
    <tab-item @on-item-click="onItemClick">组合贷</tab-item>
  </tab> -->
  <button-tab v-model="selectedType">
    <button-tab-item selected @on-item-click="onItemClick()">商业贷</button-tab-item>
    <button-tab-item @on-item-click="onItemClick()">公积金贷</button-tab-item>
    <button-tab-item @on-item-click="onItemClick()">组合贷</button-tab-item>
  </button-tab>
  <div v-show="showPage==0">

    <ShangDaiPage></ShangDaiPage>
    </keep-alive>
  </div>
  <div v-show="showPage==1">
    <GongJijinPage></GongJijinPage>
  </div>
  <div v-show="showPage==2">
    <ZuHePage></ZuHePage>
  </div>
  <div v-show="showPage==3">
    <ShuiFiePage></ShuiFiePage>
  </div>
</div>
</template>
<script>
import {
  Tab,
  TabItem,
  ButtonTab,
  ButtonTabItem,
  Divider
} from "vux";
import ShangDaiPage from "./ShangDaiPage"
import GongJijinPage from "./GongJiJinPage"
import ZuHePage from "./ZuHePage"
import ShuiFiePage from "./ShuiFiePage"
export default {
  components: {
    Tab,
    TabItem,
    ButtonTab,
    ButtonTabItem,
    Divider,
    ShangDaiPage,
    GongJijinPage,
    ZuHePage,
    ShuiFiePage
  },
  data() {
    return {
      showPage: 0,
      selectedType :0
    };
  },
  methods: {
    onItemClick() {
      this.showPage = this.selectedType;
    }
  }
};
</script>

<style>

.vux-button-group {
    -webkit-touch-callout: none;
    display: box;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    background-color: #4270d0!important;
      padding: 5%;
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
