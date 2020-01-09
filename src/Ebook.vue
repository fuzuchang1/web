<template>
   <div class="ebook">
       <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
       <div class="read-wrapper">
           <div id="read"></div>
           <div class="mask">
               <div class="left" @click="prevPage"></div>
               <div class="center" @click="toggelTitleAndMenu"></div>
               <div class="right" @click="nextPage"></div>
           </div>
       </div>
       <menu-bar 
       :ifTitleAndMenuShow="ifTitleAndMenuShow" 
       ref="menuBar"
       :fontSizeList="fontSizeList"
       :defaultFontSize="defaultFontSize"
       @setFontSize="setFontSize"
       :themeList="themeList"
       :defaultTheme="defaultTheme"
       @setTheme="setTheme"
       :bookAvailable="bookAvailable"
       @onProgressChange="onProgressChange"
       @jumpTo="jumpTo"
       :navigation="navigation"></menu-bar>
   </div>
</template>
<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/Menubar'
import Epub from 'epubjs'
const DOWNLOAD_URL = '/static/124845.epub'
global.epub = Epub
export default {
    components:{
        TitleBar,
        MenuBar
    },
    data(){
        return {
            ifTitleAndMenuShow:false,
            fontSizeList:[
                {fontSize: 12},
                {fontSize: 14},
                {fontSize: 16},
                {fontSize: 18},
                {fontSize: 20},
                {fontSize: 22},
                {fontSize: 24},
            ],
            defaultFontSize:16,
            themeList: [
                {
                    name: 'default',
                    style : {
                        body: {
                            'color':'#000',
                            'background':'#fff'
                        }
                    }
                },
                {
                    name: 'eye',
                    style : {
                        body: {
                            'color':'#000',
                            'background':'#ceeaba'
                        }
                    }
                },
                {
                    name: 'night',
                    style : {
                        body: {
                            'color':'#fff',
                            'background':'#000'
                        }
                    }
                },
                {
                    name: 'gold',
                    style : {
                        body: {
                            'color':'#000',
                            'background':'rgb(241, 236, 226)'
                        }
                    }
                }
            ],
            defaultTheme:0,
            bookAvailable:false,
            navigation:{}
        }
    },
    methods:{
        //根据链接跳转到指定位置
        jumpTo(target){
            this.rendition.display(target)
            this.hideTitleAndMenu()
        },
        hideTitleAndMenu(){
            //隐藏标题栏和菜单栏
            this.ifTitleAndMenuShow = false
            //隐藏菜单栏弹出的设置栏
            this.$refs.menuBar.hideSetting()
            //隐藏目录
            this.$refs.menuBar.hideContent()
        },
        //progress精度条的数值（0-100）
        onProgressChange(progress){
            const percentage = progress / 100
            const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
            this.rendition.display(location)
        },
        setTheme(index){
            this.themes.select(this.themeList[index].name)
            this.defaultTheme = index
        },
        registerTheme(){
            this.themeList.forEach(item => {
                this.themes.register(item.name, item.style)
            })
        },
        setFontSize(fontSize){
            this.defaultFontSize = fontSize
            if(this.themes){
                this.themes.fontSize(fontSize + 'px')
            }
        },
        toggelTitleAndMenu(){
            this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
            if(!this.ifTitleAndMenuShow){
                this.$refs.menuBar.hideSetting()
            }
        },
        prevPage(){
            if(this.rendition){
                //电子书下一页
                this.rendition.prev()
            }
        },
        nextPage(){
            if(this.rendition){
                //电子书上一页
                this.rendition.next()
            }
        },
        //电子书的解析跟渲染
        showEpub(){
            //生成book
            this.book = new Epub(DOWNLOAD_URL)
            //生成Rendition 通过Book.renderTo
            this.rendition = this.book.renderTo('read', {
                width: window.innerWidth,
                height: window.innerHeight
            })
            //通过生成Rendition.display()渲染电子书
            this.rendition.display()

            //获取Theme对象
            this.themes = this.rendition.themes
            //设置默认字体
            this.setFontSize(this.defaultFontSize)

            //设置背景
            this.registerTheme()
            // this.themes.select('night')
            this.setTheme(this.defaultTheme)

            //通过epubjs的钩子函数laishixian
            this.book.ready.then(() => {
                this.navigation = this.book.navigation
                // console.log(this.navigation)
                return this.book.locations.generate()
                
            }).then(result => {
                // console.log(result)
                this.locations = this.book.locations
                this.bookAvailable = true
                // this.onProgressChange(10)
            })
        }
    },
    mounted(){
        this.showEpub()
    }
}
</script>
<style lang='scss' scoped>
@import './assets/style/global';
.ebook{
    position: relative;
    
    .read-wrapper{
        .mask{
            position: absolute;
            top: 0;
            left: 0;
            z-index: 100;
            display: flex;
            width: 100%;
            height: 100%;
            .left{
                flex: 0 0 px2rem(100);
            }
            .center{
                flex: 1;
            }
            .right{
                flex: 0 0 px2rem(100);
            }
        }
    }
}
</style>

