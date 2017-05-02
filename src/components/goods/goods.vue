<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul>
                <li v-for='(item,index) in goods' class="menu-item" @click="setMenu(index,$event)" :class="{active:currentIndex===index}">
                    <span class="text border-1px ">
                        <span v-if='item.type>0' class="icon" :class="classMap[item.type]"></span>{{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodWrapper">
            <ul>
                <li v-for="item in goods" ref="foodHook">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                        <li v-for="food in item.foods" class="food-item">
                            <div class="icon">
                                <img width="57px" height="57px" :src="food.icon">
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="desc" v-show="food.description!==''">{{food.description}}</p>
                                <div class="extra">
                                    <span class="sell-count">月售{{food.sellCount}}份</span>
                                    <span>好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span class="now-price">¥{{food.price}}</span><span v-show="food.oldPrice!==''" class="old-price">¥{{food.oldPrice}}</span>
                                </div>
                                <div class="cartcontrol-wrapper">
                                    <cartcontrol :food="food"></cartcontrol>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shopcart @empty="empty" :delivery-price="seller.deliveryPrice" :min-price='seller.minPrice' :selectFoods="selectFood"></shopcart>
    </div>
</template>
<script>
    import BScroll from 'better-scroll';
    import cartcontrol from '@/components/cartcontrol/cartcontrol';
    import shopcart from '@/components/shopcart/shopcart';
    export default{
        props: {
            seller: {
                type: Object
            }
        },
        components: {
            cartcontrol,
            shopcart
        },
        data() {
            return {
                goods: [],
                listHeight: [],
                scrollY: 0
            };
        },
        created() {
            this.$http.get('/api/goods').then((response) => {
                if (response.body.errno === 0) {
                    this.goods = response.body.data;
                    this.$nextTick(() => {
                        this._initScroll();
                        this._calculateHeight();
                    });
                }
            });
            this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
        },
        computed: {
            selectFood() {
                let foods = [];
                this.goods.forEach((good) => {
                    good.foods.forEach((food) => {
                        if (food.count) {
                            foods.push(food);
                        }
                    });
                });
                return foods;
            },
            currentIndex() {
                for (let i = 0; i < this.listHeight.length; i++) {
                    let height1 = this.listHeight[i];
                    let height2 = this.listHeight[i + 1];
                    if (!height2 || (this.scrollY < height2 && this.scrollY >= height1)) {
                        return i;
                    }
                }
                    return 0;
            }
        },
        methods: {
            _initScroll() {
                this.meunScroll = new BScroll(this.$refs.menuWrapper, {
                    click: true
                });
                this.foodScroll = new BScroll(this.$refs.foodWrapper, {
                    probeType: 3,
                    click: true
                });
                this.foodScroll.on('scroll', (pos) => {
                    this.scrollY = Math.abs(Math.round(pos.y));
                });
            },
            _calculateHeight() {
                let foodList = this.$refs.foodHook;
                let height = 0;
                this.listHeight.push(height);
                for (let i = 0; i < foodList.length; i++) {
                    let item = foodList[i];
                    height += item.clientHeight;
                    this.listHeight.push(height);
                }
            },
            setMenu(index, event) {
                if (!event._constructed) {
                    return;
                }
               let foodList = this.$refs.foodHook;
               let el = foodList[index];
               this.foodScroll.scrollToElement(el, 250);
            },
            empty() {
                this.goods.forEach((item) => {
                    item.foods.forEach((food) => {
                        if (food.count) {
                            food.count = 0;
                        }
                    });
                });
            }
        }
    };
</script>
<style lang="stylus" rel='stylesheet/stylus'>
@import '../../common/stylus/mixin.styl'
    .goods
        display:flex
        position:absolute
        top:174px
        bottom:46px
        width:100%
        overflow:hidden
        .menu-wrapper
            flex:0 0 80px
            width:80px
            background:#f3f5f7
            overflow:hidden
            .menu-item
                display:table
                height:54px
                width:56px
                line-height:14px
                padding:12px
                .icon
                    display:inline-block
                    vertical-align:top
                    width:12px
                    height:12px
                    margin-right:2px
                    background-size:12px 12px
                    background-repeat:no-repeat
                    &.decrease
                        bg-image('decrease_3')
                    &.discount
                        bg-image('discount_3')
                    &.guarantee
                        bg-image('guarantee_3')
                    &.invoice
                        bg-image('invoice_3')
                    &.special
                        bg-image('special_3')
                .text
                    display:table-cell
                    vertical-align:middle
                    width:56px
                    font-size:12px
                    text-align:center
                    border-1px(rgba(7,17,27,0.1))
                &.active
                    position:relative
                    font-weight:400
                    z-index:10
                    margin-top:-1px
                    background:white
                    .text
                        border:none
                    
        .foods-wrapper
            flex:1
            .title
                height:26px
                padding-left:14px
                font-size:12px
                line-height:26px
                color:rgb(147,153,159)
                border-left:2px solid #d9dde1
                background:#f3f5f7
            .food-item
                display:flex
                margin:18px
                padding-bottom:18px
                border-1px(rgba(7,17,27,0.1))
                &:last-child
                    border-none()
                    margin-bottom:0
                .icon
                    flex:0 0 57px
                    margin-right:10px
                .content
                    flex:1
                    font-size:10px
                    color:rgb(147,153,159)
                    line-height:10px
                    .name
                        margin:2px 0 8px 0
                        font-size:14px
                        line-height:14px
                        color:rgb(7,17,27)
                    .desc
                        margin-bottom:8px
                        line-height:12px
                    .extra
                        margin-bottom:8px
                        .sell-count
                            margin-right:12px
                    .price
                        line-height:24px
                        .now-price
                            font-size:14px
                            color:rgb(240,20,20)
                            margin-right:8px
                            font-weight:700
                        .old-price
                            text-decoration:line-through
                            font-weight:700
                    .cartcontrol-wrapper
                        position:absolute
                        right:0
                        bottom:12px



</style>