<template>
    <transition name="slide-right">
        <div class="content">
            <div class="content-wrapper" v-if="bookAvailable">
                <div class="content-item" v-for="(item , index) in navigation.toc" :key="index" @click="jumpTo(item.href)">
                    <span class="text">{{item.label}}</span>
                </div>
            </div>
            <div class="empty" v-else>加载中...</div>
        </div>
    </transition>
</template>

<script>
export default {
   props:{
       ifShowContent:{
            type: Boolean,
            default: false
        },
        navigation:Object,
        bookAvailable:{
            type: Boolean,
            default: false
        }
   },
   methods:{
       jumpTo(target){
           this.$emit('jumpTo',target)
       }
   }
}
</script>
<style lang='scss' scoped>
@import '../assets/style/global';
.content{
    width: 70%;
    height: 100%;
    background: white;
    position: absolute;
    left: 0;
    top: 0;
    z-index: 102;
    overflow: hidden;
    overflow: scroll;
    .content-wrapper{
        padding: px2rem(20);
        .content-item{
            width: 100%;
            height: px2rem(30);
            line-height: px2rem(30);
            font-size: px2rem(18);
            border-bottom: px2rem(1) solid #ddd;
        }
    }
    .empty{
        width: 100%;
        height: 100%;
        font-size: px2rem(16);
        @include center;
    }
}
</style>