<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar" alt="" >
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div class="supports" v-if="seller.supports">
          <icon :type="seller.supports[0].type" :size="12" :imgSize="1"></icon>
          <!--<span class="icon" :class="classMap[seller.supports[0].type]"></span>-->
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="support-count" v-if="seller.supports" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" alt="" width="100%" height="100%">
    </div>
    <transition  name="fade">
      <div v-show="detailShow" class="detail" >
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <!--eslint-disable-next-line-->
              <li class="support-item" v-for="item in seller.supports">
                <icon :type="item.type" :size="16" :imgSize="2"></icon>
                <!--<span class="icon" :class="classMap[item.type]"></span>-->
                <span class="text">{{item.description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <p class="content">{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hideDetail">
          <i class="icon-close"></i>
        </div>
      </div>

    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from 'components/star/star';
  import icon from 'components/icon/icon';
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        detailShow: false
      };
    },
    methods: {
      showDetail() {
        this.detailShow = true;
      },
      hideDetail() {
        this.detailShow = false;
      }
    },
    created() {
      // this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    components: {
      star,
      icon
    }
  };
</script>

<style lang="less" rel="stylesheet/less">
  @import "../../common/less/mixin";
  .header{
    color: #ffffff;
    position :relative;
    background :rgba(7,17,27,.5);
    overflow :hidden;
    .content-wrapper{
      padding : 24/@num 12/@num 18/@num 24/@num;
      font-size :0;
      position :relative;
      .avatar{
        display :inline-block;
        vertical-align :top;
        img{
          border-radius :2/@num;
          width :64/@num;
          height :64/@num;
        }
      }
      .content{
        display :inline-block;
        margin-left :16/@num;
        .title{
          margin :2/@num 0 8/@num 0;
          .brand{
            vertical-align :top;
            display :inline-block;
            width :30/@num;
            height :18/@num;
            .bg-image('brand');
            background-size :30/@num 18/@num;
            background-repeat :no-repeat;
          }
          .name{
            margin-left :6/@num;
            font-size :16/@num;
            line-height :18/@num;
            font-weight :bold;
          }
        }
        .description{
          margin-bottom :10/@num;
          line-height :12/@num;
          font-size :12/@num;
        }
        .supports{
          .text{
            line-height :12/@num;
            font-size :10/@num;
          }
        }
      }
      .support-count{
        position :absolute;
        right :12/@num;
        bottom :14/@num;
        padding :0 8/@num;
        height :24/@num;
        line-height :24/@num;
        border-radius :14/@num;
        background :rgba(0,0,0,.2);
        text-align :center;
        .count{
          vertical-align :top;
          font-size :10/@num;
        }
        .icon-keyboard_arrow_right{
          font-size :10/@num;
          line-height :24/@num;
          margin-left :2/@num;
        }
      }
    }
    .bulletin-wrapper{
      position :relative;
      height :28/@num;
      line-height :28/@num;
      padding : 0 22/@num 0 12/@num;
      white-space :nowrap;
      overflow :hidden;
      text-overflow :ellipsis;
      font-size : 12/@num;
      background :rgba(7,17,27,.2);
      .bulletin-title{
        vertical-align :top;
        display :inline-block;
        margin-top :8/@num;
        width :22/@num;
        height :12/@num;
        .bg-image('bulletin');
        background-size :22/@num 12/@num;
        background-repeat :no-repeat;
      }
      .bulletin-text{
        vertical-align :top;
        font-size :10/@num;
        margin :0 4/@num;
      }
      .icon-keyboard_arrow_right{
        position :absolute;
        font-size :10/@num;
        right :12/@num;
        top:8/@num;
      }
    }
    .background{
      position :absolute;
      top:0;
      left :0;
      width :100%;
      height :100%;
      z-index :-1;
      filter :blur(10px);
    }
    .detail{
      position :fixed;
      z-index :100;
      top :0;
      left :0;
      width :100%;
      height :100%;
      overflow :auto;
      transition: all .5s;
      background :rgba(7,17,27,.8);
      backdrop-filter:blur(10px);
      &.fade-enter-active{
        /*transition: all .5s;*/
        opacity: 1;
        background :rgba(7,17,27,.8);
      }
      &.fade-enter,&.fade-leave{
        opacity: 0;
        background: rgba(7,17,27,0);
      }
      &.fade-leave-active{
        /*transition: all .5s;*/
        opacity: 0;
        background: rgba(7,17,27,0);
      }
      .detail-wrapper{
        min-height:100%;
        width: 100%;
        .detail-main{
          margin-top :64/@num;
          padding-bottom:64/@num;
          .name{
            line-height: 16/@num;
            text-align: center;
            font-size: 16/@num;
            font-weight: 700;
          }
          .star-wrapper{
            margin-top: 18/@num;
            padding: 2/@num 0;
            text-align: center;
          }
          .title{
            display: flex;
            width: 80%;
            margin: 28/@num auto 24/@num auto;
            .line{
              flex: 1;
              position: relative;
              top: -6/@num;
              border-bottom: 1px solid rgba(255,255,255,.2);
            }
            .text{
              padding:0 12/@num;
              font-weight: 700;
              font-size: 14/@num;
            }
          }
          .supports{
            width: 80%;
            margin: 0 auto;
            .support-item{
              padding: 0 12/@num;
              margin-bottom: 12/@num;
              font-size: 0;
              &:last-child{
                margin-bottom: 0;
              }
              .text{
                line-height: 12/@num;
                font-size: 12/@num;
              }
            }
          }
          .bulletin{
            width: 80%;
            margin: 0 auto;
            .content{
              padding: 0 12/@num;
              line-height: 24/@num;
              font-size: 12/@num;
            }
          }
        }
      }
      .detail-close{
        position :relative;
        width :32/@num;
        height :32/@num;
        margin :-64/@num auto 0 auto;
        clear :both;
        font-size :32/@num;
      }
    }
  }
</style>
