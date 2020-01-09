<template>
   <transition name="slide-down">
        <div class="title-wrapper" v-show="ifTitleAndMenuShow">
            <div class="left">
                <span class="icon-home icon"></span>
            </div>
            <div class="right">
                <div class="icon-wrapper">
                    <span class="icon-image icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-books icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-books icon"></span>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
export default {
    props: {
        ifTitleAndMenuShow:{
            type: Boolean,
            default: false
        }
    }
}
</script>
<style lang='scss' scoped>
@import '../assets/style/global';
.title-wrapper{
    position: absolute;
    top:0;
    left: 0;
    width: 100%;
    z-index: 101;
    display: flex;
    height: px2rem(48);
    background: white;
    box-shadow: 0 px2rem(4) px2rem(4) rgba(0,0,0,.15);
    .left{
        flex: 0 0 px2rem(60);
        @include center;
    }
    .right{
        flex: 1;
        display: flex;
        justify-content: flex-end;
        .icon-wrapper{
            flex: 0 0 px2rem(40);
            @include center;
        }
    }
    
}
</style>