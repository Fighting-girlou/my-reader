<template>
    <div class="ebook">
        <titleBar :iftitleAndmenuShow='iftitleAndmenuShow'></titleBar>
        <div id="read">
            <div class="mask">
                <div class="left" @click="prevPage"></div>
                <div class="center" @click="showWarp"></div>
                <div class="right" @click="nextPage"></div>
            </div>
        </div>
         <menuBar :iftitleAndmenuShow='iftitleAndmenuShow' ref="menuBar"
          :fontsizeSelect='fontsizeSelect' :DefaultfontSize="DefaultfontSize" @setFontSize="setFontSize"
          :DefaultTheme="DefaultTheme" @setTheme="setTheme"
          :ThemesList="ThemesList"
          :bookAvailable="bookAvailable" @onProgressChange="onProgressChange"
          @jumpTo="jumpTo" 
          :navigation="navigation"></menuBar>
    </div>
</template>

<script>
import menuBar from '@/components/menuBar'
import titleBar from '@/components/titleBar'
import Epub from 'epubjs'
const DOMLOAD_URL='./static/ebook.epub'
global.epub = Epub
export default {
    components: {
       menuBar,
       titleBar
    },
    data () {
        return {
            iftitleAndmenuShow:false,
            fontsizeSelect:[
                {fontSize:12},
                {fontSize:14},
                {fontSize:16},
                {fontSize:18},
                {fontSize:20},
                {fontSize:22},
                {fontSize:24}
            ],
            DefaultfontSize:16 ,
            ThemesList:[
                {
                    name:"default",
                    style:{
                        body:{
                        "color":"#000",
                        "background":"#fff"
                        }
                    }
                },
                {
                    name:"eye",
                    style:{
                        body:{
                        "color":"#000",
                        "background":"#ceeaba"
                        }
                    }
                },
                {
                    name:"night",
                    style:{
                        body:{
                        "color":"#fff",
                        "background":"#000"
                        }
                    }
                },
                {
                    name:"gold",
                    style:{
                        body:{
                        " color":"#000",
                        "background":"rgb(241,236,226)"
                        }
                    }
                }
            ],
            DefaultTheme:0,
            bookAvailable:false,
            navigation:null
        }
    },
    methods: {
        //点击目录时跳转链接
        jumpTo(href) {
            this.rendition.display(href)
            this.hideTitleAndMenu()
        },
        hideTitleAndMenu (){
            this.iftitleAndmenuShow = false
            this.$refs.menuBar.hideSetting()
            this.$refs.menuBar.hideContent()
        },
        onProgressChange(progress) {
            const percentage = progress / 100
            const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage): 0
            this.rendition.display(location)
        },
        setTheme (index){
            this.themes.select(this.ThemesList[index].name)
            this.DefaultTheme = index;
        },
        registerThemes (){
            this.ThemesList.forEach( theme => {
                this.themes.register(theme.name,theme.style)
            })
        },
        setFontSize (fontSize){
            this.DefaultfontSize = fontSize;
            if(this.themes){
                this.themes.fontSize(fontSize+'px');
                //相当于给fontSize赋值，所以要加上px才会生效
            }
        },
      // 电子书的解析和渲染
        showEpub () {
          // 生成Book对象
          this.book = new Epub(DOMLOAD_URL)
          // 生成Rendition对象，通过Book.renderTo方法
          this.rendition = this.book.renderTo('read', {
            width: window.innerWidth,
            height: window.innerHeight
          })
          // 通过Rendtion.display渲染电子书
          this.rendition.display()
          // 获取到Theme对象，用来对字体大小进行设置
          this.themes = this.rendition.themes
          // 调用设置默认字体的方法
          this.setFontSize(this.defaultFontSize)
          // 调用主题设置的方法
          this.registerThemes()
          // 调用主题样式的方法
          this.setTheme(this.defaultTheme)
          // 获取Locations对象，实现进度条和目录功能
          // 通过epubjs的钩子函数来实现
          this.book.ready.then(() => {
            this.navigation = this.book.navigation
            return this.book.locations.generate()
          }).then(result => {
            this.locations = this.book.locations
            this.bookAvailable = true
          })
        },
        // 实现翻上一页
        prevPage () {
          if (this.rendition) {
            this.rendition.prev()
          }
        },
        // 实现翻下一页
        nextPage () {
          if (this.rendition) {
            this.rendition.next()
          }
        },
        showWarp (){
            this.iftitleAndmenuShow=!this.iftitleAndmenuShow
            if(!this.iftitleAndmenuShow){
                this.$refs.menuBar.hideSetting()
                //ref属性是绑定节点
            }
        }
    },
    mounted (){
      this.showEpub()
    }
}
</script>

<style lang="scss" scoped>
@import 'assets/style/global.scss';
@import 'assets/style/font/iconfont.css';
.ebook {
    position: relative;
        /*上面标题栏，用&符号表示与title同级*/
        /*&.slide-down-enter,&.slide-down-leave-to{ */
             /*transform: translate3d(0,-100%,0);*/
         /*}*/
         /*&.slide-down-enter-to,&.slide-down-leave {*/
            /*transform: translate3d(0,0,0);*/
         /*}*/
        /*&.slide-down-enter-active,&.slide-down-leave-active {*/
             /*transition: all 0.3s linear;*/
         /*}*/
    #read {
        .mask {
            position: absolute;
            top:0;
            left: 0;
            z-index: 100;
            display: flex;
            width: 100%;
            height: 100%;
            .left {
                flex: 0 0 px2rem(200);
            }
            .center {
                flex:1;

            }
            .right {
                flex:0 0 px2rem(200);

            }
        }
    }
}
</style>
