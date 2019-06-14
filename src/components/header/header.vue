<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar" width="64" height="64">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}} / {{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: [
      'seller'
    ],
    data () {
      return {
        classMap: []
      };
    },
    methods: {

    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    }
  };
</script>

<style scoped lang="scss">
  @import '@/common/style/mixin.scss';
  .header{
    color: #fff;
    background: #000;
    .content-wrapper{
      padding: 24px 12px 18px 24px;
      font-size: 0;// 空白字符
      .avatar{
        display: inline-block;
        vertical-align: top;
        img{
          border-radius: 2px;
        }
      }
      .content{
        display: inline-block;
        margin-left: 16px;
        .title{
          margin: 2px 0 8px 0;
          .brand{
            display: inline-block;
            vertical-align: top;
            width: 30px;
            height: 18px;
            @include bg-image('brand');
            background-size: 30px 18px!important;
            background-repeat: no-repeat!important;
          }
          .name{
            margin-left: 6px;
            font-size: 16px;
            line-height: 18px;
            font-weight: bold;
          }
        }
        .description{
          margin-bottom: 12px;
          line-height: 12px;
          font-size: 12px;
        }
        .support{
          .icon{
            display: inline-block;
            vertical-align: top;
            width: 12px;
            height: 12px;
            margin-right: 4px;
            &.decrease{
              @include bg-image('decrease_1');
            }
            &.discount{
              @include bg-image('discount_1');
            }
            &.guarantee{
              @include bg-image('guarantee_1');
            }
            &.invoice{
              @include bg-image('invoice_1');
            }
            &.special{
              @include bg-image('special_1');
            }
            background-size: 12px 12px!important;
            background-repeat: no-repeat!important;
          }
          .text{
            line-height: 12px;
            font-size: 12px;
          }
        }
      }
    }
  }
</style>