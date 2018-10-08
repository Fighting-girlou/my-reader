<template>
<transition name="slide-down">
    <div class="title"
    v-show="iftitleAndmenuShow">
        <div class="left">
            <div class="icon-warp">
                <span class="iconfont icon-back icon"></span>
            </div>
        </div>
        <div class="right">
            <div class="icon-warp">
                <span class="iconfont icon-person icon"></span>
            </div>
            <div class="icon-warp">
                <span class="iconfont icon-cart icon"></span>
            </div>
            <div class="icon-warp">
                <span class="iconfont icon-more icon"></span>
            </div>
        </div>
    </div>
</transition>
</template>

<script type="text/ecmascript-6">
export default {
    props:{
        iftitleAndmenuShow:{
            type:Boolean,
            default:false
        }
    },
 data() {
 return {

 }
 },
 components: {

 }
}
</script>

<style lang="scss" scoped>
@import '../assets/style/global.scss';
@import '../assets/style/font/iconfont.css';
.title {
        position: absolute;
        top: 0;
        left:0;
        width: 100%;
        height:px2rem(48);
        z-index: 101;
        display: flex;
        background-color: #fff;
        box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,.15);
        .left {
           flex: 0 0 px2rem(60);
           @include center; 
        }
        .right {
            flex:1;
            display: flex;
            justify-content: flex-end;
            .icon-warp {
                flex:0 0 px2rem(40);
                @include center;
            }
        }
}
</style>
