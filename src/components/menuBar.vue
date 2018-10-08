<template>
  <div class="menu-bar">
    <transition name="slide-up">
        <div class="menu" :class="{'hideBoxShadow':ifsettingshow||!iftitleAndmenuShow}"
        v-show="iftitleAndmenuShow">
            <div class="icon-warp">
                <span class="iconfont icon-menu icon" @click="showSetting(3)"></span>
            </div>
            <div class="icon-warp">
                <span class="iconfont icon-progress_icon icon" @click="showSetting(2)"></span>
            </div>
            <div class="icon-warp">
                <span class="iconfont icon-brightness icon" @click="showSetting(1)"></span>
            </div>
            <div class="icon-warp">
                <span class="iconfont icon-A icon" @click="showSetting(0)"></span>
            </div>
        </div>
    </transition>
    <transition name="slide-up">
        <div class="setting-warp" v-show="ifsettingshow">
            <div class="setting-font-size" v-if="showTag==0">
                <div class="preview" :style="{fontSize:fontsizeSelect[0].fontSize+'px'}">A</div>
                <div class="selectAll">
                    <div class="select" v-for="(item,index) in fontsizeSelect" :key="index"
                     @click="setfontSize(item.fontSize)">
                        <div class="line"></div>
                        <div class="point-warp">
                            <div class="point" v-show="DefaultfontSize===item.fontSize">
                                <div class="small-point"></div>
                            </div>
                        </div>
                        <div class="line"></div>
                    </div>
                </div>
                <div class="preview" :style="{fontSize:fontsizeSelect[fontsizeSelect.length-1].fontSize+'px'}">A</div>
            </div>
            <div class="setting-theme" v-else-if="showTag==1">
                <div class="setting-theme-item" v-for="(item,index) in ThemesList" :key="index"
                @click="setTheme(index)">
                    <div class="themes" :style="{background: item.style.body.background}"
                    :class="{'hideBorder':item.style.body.background!='#fff'}"></div>
                    <div class="text" :class="{'selected':index==DefaultTheme}">{{item.name}}</div>
                </div>
            </div>
            <div class="setting-progress" v-else-if="showTag==2">
                <div class="progress-warp">
                    <input class="progress" type="range"
                                            max="100"
                                            min="0"
                                            step="1"
                                            @change="onProgressChange($event.target.value)"
                                            @input="onProgressInput($event.target.value)"
                                            :value="progress"
                                            :disabled="!bookAvailable"
                                            ref="progress"/>
                </div>
                <div class="text-warp">
                    <span>{{bookAvailable ? progress +"%":"文章正在加载中......"}}</span>
                </div>
            </div>
        </div>
        
    </transition>
        <content-view :ifshowContent="ifshowContent"
                      v-show="ifshowContent"
                      :navigation="navigation"
                      :bookAvailable="bookAvailable"
                      @jumpTo="jumpTo">
        </content-view>
    <transition name="fade">
        <div class="mask" v-show="ifshowContent" @click="hideContent">
        </div>
    </transition>
    
    
</div>
</template>

<script>
import contentView from "@/components/content"
export default {
    components:{
        contentView
    },
    props:{
        iftitleAndmenuShow:{
            type:Boolean,
            default:false
        },
        fontsizeSelect: Array,
        DefaultfontSize: Number,
        ThemesList:Array,
        DefaultTheme:Number,
        bookAvailable:{
            type:Boolean,
            default:false
        },
        navigation: Object
    },
    data() {
        return {
            ifsettingshow:false,
            showTag:0,
            progress:0,
            ifshowContent:false
        };
    },
    methods:{
        //隐藏蒙版
        hideContent() {
            this.ifshowContent = false
        },
        //拖动进度条时发生的事件
        onProgressInput(progress) {
            this.progress = progress
            this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
        },
        //进度条松手时触发的事件
        onProgressChange(progress) {
            this.$emit("onProgressChange",progress)
        },
        setTheme (index){
            this.$emit('setTheme',index)
        },
        setfontSize (fontSize){
            this.$emit('setFontSize',fontSize);
        },
        showSetting (Tag){
            this.showTag = Tag
            if(Tag==3){
                this.ifsettingshow = false
                this.ifshowContent = true
            }else{
                this.ifsettingshow = true
                
            }
            
        },
        hideSetting (){
            this.ifsettingshow = false
        },
        jumpTo(target) {
            this.$emit("jumpTo",target)
        }
    }
}
</script>

<style lang="scss" scoped>
@import '../assets/style/global.scss';
@import '../assets/style/font/iconfont.css';
.menu-bar {
    .menu {
        position: absolute;
        width: 100%;
        height: px2rem(48);
        left: 0;
        bottom: 0;
        z-index: 101;
        display: flex;
        background-color: #fff;
        box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
        &.hideBoxShadow {
            box-shadow: none;
        }
        .icon-warp {
            flex:1;
            @include center;
        }
    }
    .setting-warp {
        position: absolute;
        left:0;
        bottom: px2rem(48);
        z-index: 101;
        width: 100%;
        height: px2rem(48);
        background-color:white;
        box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
        .setting-font-size {
            display: flex;
            height: 100%;
            .preview {
                flex: 0 0 px2rem(40);
                @include center;
            }
            .selectAll{
                display: flex;
                flex:1;
                .select{
                    flex: 1;
                    display: flex;
                    align-items: center;
                    &:first-child{
                        .line {
                            &:first-child{
                                border-top:none;
                            }
                        }
                    }
                    &:last-child{
                        .line{
                            &:last-child {
                                border-top:none;
                            }
                        }
                    }
                    .line {
                        flex:1;
                        height: 0;
                        border-top:px2rem(1) solid #cccccc;
                    }
                    .point-warp {
                        flex:0 0 0;
                        width:0;
                        height: px2rem(8);
                        border-left:px2rem(1) solid #ccc;
                        position: relative;
                        .point {
                            width: px2rem(20);
                            height: px2rem(20);
                            border: px2rem(1) solid #ccc;
                            border-radius: 50%;
                            background-color: white;
                            position: absolute;
                            top:px2rem(-8);
                            left:px2rem(-8);
                            box-shadow: 0 px2rem(6) px2rem(6) rgba(0,0,0,.15);
                            @include center;
                            .small-point {
                                width: px2rem(6);
                                height: px2rem(6);
                                background-color: black;
                                border-radius: 50%;
                                
                            }
                        }
                    }
                }
            }
            
        }
        .setting-theme {
            display: flex;
            height: 100%;
            .setting-theme-item {
                flex:1;
                display: flex;
                flex-direction: column;
                box-sizing: border-box;
                padding: px2rem(5);
                .themes {
                    flex: 1;
                    border:px2rem(1) solid #ccc;
                    box-sizing: border-box;
                    &.hideBorder {
                        border:none;
                    }  
                }
                .text {
                    flex:0 0 px2rem(20);
                    font-size: px2rem(18);
                    color: #999;
                    @include center;
                    &.selected {
                        color:black;
                    }
                }
            }
        }
        .setting-progress {
            width: 100%;
            height: 100%;
            position: relative;
            .progress-warp {
                width: 100%;
                height: 100%;
                padding: 0 px2rem(30);
                box-sizing: border-box;
                @include center;
                .progress {
                    width: 100%;
                    -webkit-appearance: none;
                    height: px2rem(3);
                    background: -webkit-linear-gradient(#999,#999) no-repeat, #ddd;
                    background-size: 0 100%;
                    &:focus {
                        outline: none;
                    }
                    &::-webkit-slider-thumb {
                       -webkit-appearance: none;
                       //去掉之前原有的外观
                       width: px2rem(20); 
                       height: px2rem(20);
                       border-radius: 50%;
                       background: #ffffff;
                       box-shadow: 0 px2rem(4) px2rem(4) rgba(0,0,0,.15);
                       border: px2rem(1) solid #ccc;
                    }
                }
            }
            .text-warp {
                width: 100%;
                height: px2rem(20);
                @include center;
                span {
                    font-size: px2rem(18);
                }
            }
        }
    }
    .mask {
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0;
        left: 0;
        display: flex;
        z-index: 101;
        background: rgba(51,51,51,0.8)
    }

}

</style>
