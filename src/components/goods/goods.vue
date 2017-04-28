<template>
    <div class="goods">
        <div class="menu-wrapper">
            <ul>
                <li v-for='item in goods' class="menu-item">
                    <span class="text border-1px" >
                        <span v-if='item.type>0' class="icon" :class="classMap[item.type]"></span>{{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper"></div>
    </div>
</template>
<script>
    export default{
        props: {
            seller: {
                type: Object
            }
        },
        data() {
            return {
                goods: []
            };
        },
        created() {
            this.$http.get('/api/goods').then((response) => {
                if (response.body.errno === 0) {
                    this.goods = response.body.data;
                }
            });
            this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
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
            overflow-y:auto
            overflow-x:hidden
            .active
                line-height:14px
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
                    width:56px;
                    font-size:12px
                    font-weight:200
                    text-align:center
                    border-1px(rgba(7,17,27,0.1))
                    
        .foods-wrapper
            flex:1
            
                            



</style>