<template>
    <div class="shopcart">
        <div class="content" @click="toggleList">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'highlight':totalCount>0}">
                        <i class="el-icon-shopping-cart-2" :class="{'highlight':totalCount>0}"></i>
                    </div>
                    <div class="num" v-show="totalCount>0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}元</div>
                <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
            </div>
            <div class="content-right">
                <div class="pay" :class="payClass">
                    {{payDesc}}
                </div>
            </div>
        </div>
        <div class="ball-container">
            <div v-for="(ball,index) in balls" :key="index">
                <transition
                    @before-enter="beforeDrop"
                    @enter="dropping"
                    @after-enter="afterDrop">
                    <div v-show="ball.show" class="ball">
                        <div class="inner inner-hook"></div>
                    </div>
                </transition>
            </div>
        </div>
        <transition name="show-detail">
            <div class="shopcart-list" v-show="totalCount&&listShow">
                <div class="list-header">
                    <h1 class="title">购物车</h1>
                    <span class="empty">清空</span>
                </div>
                <div class="list-content">
                    <ul>
                        <li class="food" v-for="(food,index) in selectFoods" :key="index">
                            <span class="name">{{food.name}}</span>
                            <div class="price">
                                <span>￥{{food.price*food.count}}</span>
                            </div>
                            <div class="cartcontrol-wrapper">
                                <cartcontrol :food="food"></cartcontrol>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    import cartcontrol from '@/components/cartcontrol/cartcontrol.vue';
    const BALL_LEN = 10;
    const innerClsHook = 'inner-hook';
    function createBalls () {
        let ret = [];
        for (let i = 0; i < BALL_LEN; i++) {
            ret.push({show: false});
        }
        return ret;
    }
    export default {
        props: ['deliveryPrice', 'minPrice', 'selectFoods'],
        components: {
            cartcontrol
        },
        data () {
            return {
                balls: createBalls(),
                fold: false,
                listShow: false
            };
        },
        created () {
            this.dropBalls = [];
        },
        methods: {
            toggleList () {
                if (!this.totalCount) {
                    this.fold = false;
                    this.listShow = false;
                } else {
                    this.fold = !this.fold;
                    if (this.fold) {
                        this.listShow = true;
                    } else {
                        this.listShow = false;
                    }
                }
            },
            beforeDrop (el) {
                // 拿到最后一个也就是最后点击的(最后下落的)那个小球
                const ball = this.dropBalls[this.dropBalls.length - 1];
                // 拿到小球位置
                const rect = ball.el.getBoundingClientRect();
                const x = rect.left - 32;
                const y = -(window.innerHeight - rect.top - 22);
                el.style.display = '';
                el.style.transform = el.style.webkitTransform = `translate3d(0, ${y}px, 0)`;
                const inner = el.getElementsByClassName(innerClsHook)[0];
                inner.style.transform = inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`;
            },
            dropping (el, done) {
                // 触发浏览器重绘
                this._reflow = document.body.offsetHeight;
                el.style.transform = el.style.webkitTransform = `translate3d(0, 0, 0)`;
                const inner = el.getElementsByClassName(innerClsHook)[0];
                inner.style.transform = inner.style.webkitTransform = `translate3d(0, 0, 0)`;
                // 监听
                el.addEventListener('transitionend', done);
            },
            afterDrop (el) {
                const ball = this.dropBalls.shift();
                if (ball) {
                    ball.show = false;
                    el.style.display = 'none';
                }
            },
            // 接收el，小球下落的初始位置
            drop (el) {
                for (let i = 0; i < this.balls.length; i++) {
                    const ball = this.balls[i];
                    if (!ball.show) {
                        ball.show = true;
                        ball.el = el;
                        this.dropBalls.push(ball);
                        return;
                    }
                }
            }
        },
        computed: {
            totalPrice () {
                let total = 0;
                this.selectFoods.forEach((item) => {
                    total += item.price * item.count;
                });
                return total;
            },
            totalCount () {
                let count = 0;
                this.selectFoods.forEach((item) => {
                    count += item.count;
                });
                return count;
            },
            payDesc () {
                if (this.totalPrice === 0) {
                    return `￥${this.minPrice}元起送`;
                } else if (this.totalPrice < this.minPrice) {
                    let diff = this.minPrice - this.totalPrice;
                    return `还差￥${diff}元起送`;
                } else {
                    return '去结算';
                }
            },
            payClass () {
                if (this.totalPrice < this.minPrice) {
                    return 'not-enough';
                } else {
                    return 'enough';
                }
            }
        }
    };
</script>

<style scoped lang="scss">
    .shopcart{
        position: fixed;
        left: 0;
        bottom: 0;
        z-index: 50;
        width: 100%;
        height: 48px;
        .content{
            display: flex;
            background: #141d27;
            font-size: 0;
            color: rgba(255, 255, 255, 0.4);
            .content-left{
                flex: 1;
                .logo-wrapper{
                    display: inline-block;
                    vertical-align: top;
                    position: relative;
                    top: -10px;
                    margin: 0 12px;
                    padding: 6px;
                    width: 56px;
                    height: 56px;
                    box-sizing: border-box;
                    border-radius: 50%;
                    background: #141d27;
                    .logo{
                        width: 100%;
                        height: 100%;
                        border-radius: 50%;
                        background: #2b343c;
                        text-align: center;
                        &.highlight{
                            background: rgb(0, 160, 220);
                        }
                        .el-icon-shopping-cart-2{
                            line-height: 44px;
                            font-size: 24px;
                            color: #80858a;
                            &.highlight{
                                color: #fff;
                            }
                        }
                    }
                    .num{
                        position: absolute;
                        top: 0;
                        right: 0;
                        width: 24px;
                        height: 16px;
                        line-height: 16px;
                        text-align: center;
                        border-radius: 16px;
                        font-size: 9px;
                        font-weight: 700;
                        color: #fff;
                        background: rgb(240, 20, 20);
                        box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4);
                    }
                }
                .price{
                    display: inline-block;
                    vertical-align: top;
                    margin-top: 12px;
                    line-height: 24px;
                    padding-right: 12px;
                    box-sizing: border-box;
                    border-right: 1px solid rgba(255, 255, 255, 0.1);
                    font-size: 16px;
                    font-weight: 700;
                    &.highlight{
                        color: #fff;
                    }
                }
                .desc{
                    display: inline-block;
                    vertical-align: top;
                    margin: 12px 0 0 12px;
                    line-height: 24px;
                    font-size: 10px;
                }
            }
            .content-right{
                flex: 0 0 105px;
                width: 105px;
                .pay{
                    height: 48px;
                    line-height: 48px;
                    text-align: center;
                    font-size: 12px;
                    font-weight: 700;
                    &.not-enough{
                        background: #2b333b;
                    }
                    &.enough{
                        background: #00b43c;
                        color: #fff;
                    }
                }
            }
        }
        .ball-container{
            .ball{
                position: fixed;
                left: 32px;
                bottom: 22px;
                z-index: 200;
                transform: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.43);
                .inner{
                    width: 16px;
                    height: 16px;
                    border-radius: 50%;
                    background: rgb(0, 160, 220);
                    transition: all 0.4s linear;
                }
            }
        }
        .shopcart-list{
            position: absolute;
            top: 0;
            left: 0;
            z-index: -1;
            width: 100%;
            transform: translate3d(0, -100%, 0);
            &.show-detail-enter, &.show-detail-leave-to{
                transform: translate3d(0, 0, 0);
            }
            &.show-detail-enter-active, &.show-detail-leave-active{
                transition: all 1s ease;
            }
        }
    }
</style>
