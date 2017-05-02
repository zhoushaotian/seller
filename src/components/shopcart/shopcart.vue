<template>
    <div class="shopcart-wapper">
        <div class="shopcart">
            <div class="content" @click="fold=!fold">
                <div class="content-left">
                    <div class='logo-wrapper'>
                        <div class="logo" :class="{active:totalPrice>0}">
                            <i class="icon-shopping_cart"></i>
                        </div>
                        <div class="num" v-show="selectFoods.length>0">
                            {{foodNum}}
                        </div>
                    </div>
                    <div class="price" :class="{active:totalPrice>0}">
                        ¥{{totalPrice}}
                    </div>
                    <div class="desc">
                        另需配送费¥{{deliveryPrice}}元
                    </div>
                </div>
                <div class="content-right">
                    <div class="pay" :class="{active:totalPrice>=minPrice}">
                        {{desc}}
                    </div>
                </div>
            </div>
            <transition name="list">
            <div class="shopcart-list" v-show="listShow" >
                <div class="list-header">
                    <h1 class="title">购物车</h1>
                    <span class="empty" @click="empty">清空</span>
                </div>
                <div class="list-content" ref="cartList">
                    <ul>
                        <li class="list-item" v-for="item in selectFoods">
                            <span class="name">{{item.name}}</span>
                            <div class="cartcontrol-wrapper">
                                <cartcontrol :food="item"></cartcontrol>
                            </div>
                            <span class="price">{{item.price*item.count}}</span>
                        </li>
                    </ul>
                </div>
            </div>     
            </transition>
        </div>
        <transition name="mask">
                <div class="mask" v-show="listShow">
                </div>
        </transition>
    </div>
</template>
<script>
    import cartcontrol from '@/components/cartcontrol/cartcontrol';
    import BScroll from 'better-scroll';
    export default {
        props: {
            deliveryPrice: {
                type: Number
            },
            minPrice: {
                type: Number
            },
            selectFoods: {
                type: Array
            }
        },
        components: {
            cartcontrol
        },
        data() {
            return {
                fold: true
            };
        },
        computed: {
            totalPrice() {
                let total = 0;
                this.selectFoods.forEach((food) => {
                    total += food.price * food.count;
                });
                return total;
            },
            desc() {
                if (this.totalPrice === 0) {
                    return `¥${this.minPrice}起送`;
                } else if (this.totalPrice > 0 && this.totalPrice < this.minPrice) {
                    return `还差¥${this.minPrice - this.totalPrice}起送`;
                } else {
                    return `去结算`;
                }
            },
            foodNum() {
                let foodNum = 0;
                this.selectFoods.forEach((food) => {
                    foodNum += food.count;
                });
                return foodNum;
            },
            listShow() {
                if (!this.fold) {
                    if (this.foodNum === 0) {
                    this.fold = true;
                    return false;
                    }
                    this.$nextTick(() => {
                        if (!this.scroll) {
                            this.scroll = new BScroll(this.$refs.cartList, {
                                click: true
                            });
                        } else {
                            this.scroll.refresh();
                        }
                    });
                    return true;
                }
            }
        },
        methods: {
            empty() {
                this.$emit('empty');
            }
        }
    };
</script>
<style lang="stylus" rel='stylesheet/stylus'>
@import '../../common/stylus/mixin.styl'
    .shopcart
        position:fixed
        left:0
        bottom:0
        z-index:50
        width:100%
        height:48px
        .content
            display:flex
            background:#141d27
            .content-left
                flex:1
                font-size:0
                .logo-wrapper
                    display:inline-block
                    position:relative
                    top:-10px
                    margin:0 12px
                    padding:6px
                    width:56px
                    height:56px
                    box-sizing:border-box
                    vertical-align:top
                    border-radius:50%
                    background:#141d27
                    .logo
                        width:100%
                        height:100%
                        border-radius:50%
                        background:#2b343c
                        text-align:center
                        i
                            font-size:24px
                            color:#80858a
                            line-height:44px
                        &.active
                            background:#00a0dc
                            i
                                color:white
                    .num
                        position:absolute
                        right:0
                        top:0
                        z-index:100
                        width:24px
                        background:rgb(240,20,20)
                        box-shadow:0px 4px 8px 0px rgba(0,0,0,0.4)
                        font-size:9px
                        font-weight:700
                        color:white
                        line-height:16px
                        text-align:center
                        border-radius:12px
                .price
                    display:inline-block
                    vertical-align:top
                    font-size:16px
                    font-weight:700
                    color:rgba(255,255,255,0.4)
                    line-height:24px
                    margin:12px 0
                    padding-right:12px
                    border-right:1px solid rgba(255,255,255,0.1)
                    &.active
                        color:white
                .desc
                    display:inline-block
                    vertical-align:top
                    line-height:24px
                    font-weight:700
                    color:rgba(255,255,255,0.4)
                    font-size:10px
                    margin:12px 0 12px 12px
            .content-right
                flex:0 0 105px
                width:105px
                .pay
                    background:#2b333b
                    height:48px
                    line-height:48px
                    font-size:12px
                    font-weight:700
                    text-align:center
                    color:rgba(255,255,255,0.4)
                    &.active
                        background:#ff8000
                        color:white
        .shopcart-list
            position:absolute
            top:0
            left:0
            z-index:-1
            width:100%
            transform:translate3d(0,-100%,0)
            .list-header
                height:40px
                line-height:40px
                padding:0px 18px
                background:#f3f5f7
                border-bottom:1px solid rgba(7,17,27,0.1)
                .title
                    float:left
                    font-size:14px
                    color:rgb(7,17,27)
                .empty
                    display:block
                    float:right
                    font-size:12px
                    color:rgb(0,160,220)
            .list-content
                max-height:217px
                background:white
                overflow:hidden
                .list-item
                    position:relative
                    margin:0 18px
                    height:48px
                    border-1px(rgba(7,17,27,0.1))
                    line-height:24px
                    padding:12px 0
                    box-sizing:border-box
                    .name
                        color:rgb(7,17,27)
                        font-weight:700
                        font-size:14px
                    .price
                        position:absolute
                        top:12px
                        right:72px
                        padding:0 12px 0 18px
                        font-size:14px
                        font-weight:700
                        color:rgb(240,20,20)
                    .cartcontrol-wrapper
                        position:absolute
                        top:6px
                        right:0
                        height:24px
                        font-size:24px
        .list-leave-active
            transition:all 0.5s
            transform:translate3d(0,0,0)
        .list-enter-active
            transition:all 0.2s
            transform:translate3d(0,-50%,0)
        .list-enter-to
            transition:all 0.2s
            transform:translate3d(0,-100%,0)
    .mask
        position:fixed
        top:0
        left:0
        width:100%
        height:100%
        z-index:0
        background:rgba(7,17,27,0.6)
    .mask-leave-active
            transition:all 0.2s
            opacity:0.5
</style>