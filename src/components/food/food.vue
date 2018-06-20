<template>
  <transition name="move">
    <div v-show="showFlag" class="fooddetail" ref="food">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image" alt="">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">¥{{food.price}}</span>
            <span v-show="food.oldPrice" class="old">¥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food" @add="addFood"></cartcontrol>
          </div>
          <transition name="fade">
            <div class="buy" v-show="!food.count || food.count === 0" @click.stop.prevent="addFirst">加入购物车</div>
          </transition>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingselect @select="selectRating" @toggle="toggleContent" :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <!--eslint-disable-next-line-->
              <li v-for="rating in food.ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" alt="" class="avatar">
                </div>
                <div class="time">{{rating.rateTime|formatDate}}</div>
                <p class="text">
                  <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
                  {{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
          </div>
        </div>
      </div>
    </div>
  </transition>

</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import split from 'components/split/split';
  import ratingselect from 'components/ratingselect/ratingselect';
  import {formatDate} from '../../common/js/date';

  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = true;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        if (!event._constructed) {
          return;
        }
        this.$emit('add', event.target);
        Vue.set(this.food, 'count', 1);
      },
      addFood(target) {
        this.$emit('add', target);
      },
      selectRating(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      toggleContent() {
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  };
</script>

<style lang="less" rel="stylesheet/less">
  @import "../../common/less/mixin";
  .fooddetail{
    position: fixed;
    left: 0;
    top: 0;
    bottom: 48px;
    z-index: 30;
    width: 100%;
    background: #fff;
    transform: translate3d(0,0,0);
    .image-header{
      position: relative;
      width: 100%;
      height: 0;
      padding-top: 100%;
      img{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
      }
      .back{
        position: absolute;
        top: 10/@num;
        left: 0;
        .icon-arrow_lift{
          display: block;
          padding: 10/@num;
          font-size: 20/@num;
          color: #fff;
        }
      }
    }
    .content{
      position: relative;
      padding: 18/@num;
      .title{
        line-height: 14/@num;
        margin-bottom: 8/@num;
        font-size: 14/@num;
        font-weight: 700;
        color: rgb(7,17,27);
      }
      .detail{
        margin-bottom: 18/@num;
        line-height: 10/@num;
        height: 10/@num;
        font-size: 0;
        .sell-count,.rating{
          font-size: 10/@num;
          color: rgb(147,153,159);
        }
        .sell-count{
          margin-right: 12/@num;
        }
      }
      .price{
        font-weight: 700;
        line-height: 24/@num;
        .now{
          margin-right: 8/@num;
          font-size: 14/@num;
          color: rgb(240,20,20);
        }
        .old{
          text-decoration: line-through;
          font-size: 10/@num;
          color: rgb(147,153,159);
        }
      }
      .cartcontrol{
        position: absolute;
        right: 12/@num;
        bottom: 12/@num;
      }
      .buy{
        position: absolute;
        right: 18/@num;
        bottom: 18/@num;
        z-index: 10;
        height: 24/@num;
        line-height: 24/@num;
        padding:  0 12/@num;
        box-sizing: border-box;
        font-size: 10/@num;
        border-radius: 12/@num;
        color: #fff;
        background: rgb(0,160,220);
      }
      .fade-enter-active,.fade-leave-active{
        transition: all .5s;
      }
      .fade-enter,.fade-leave-active{
        opacity: 0;
      }
    }
    .info{
      padding: 18/@num;
      .title{
        line-height: 14/@num;
        margin-bottom: 6/@num;
        font-size: 14/@num;
        color: rgb(7,17,27);
      }
      .text{
        line-height: 24/@num;
        padding: 0 8/@num;
        font-size: 12/@num;
        color: rgb(77,85,93);
      }
    }
    .rating{
      padding-top: 18/@num;
      .title{
        line-height: 14/@num;
        margin-left: 18/@num;
        font-size: 14/@num;
        color: rgb(7,17,27);
      }
      .rating-wrapper{
        padding: 0 18/@num;
        .rating-item{
          position: relative;
          padding: 16/@num 0;
          .border-1px(rgba(7,17,27,.1));
          .user{
            position: absolute;
            right: 0;
            top: 16/@num;
            font-size: 0;
            line-height: 12/@num;
            .name{
              display: inline-block;
              vertical-align: top;
              font-size: 10/@num;
              color: rgb(147,153,159);
              margin-right: 6/@num;
            }
            .avatar{
              border-radius: 50%;
              width: 12/@num;
              height: 12/@num;
            }
          }
          .time{
            margin-bottom: 6/@num;
            line-height: 12/@num;
            font-size: 10/@num;
            color: rgb(147,153,159);
          }
          .text{
            line-height: 16/@num;
            font-size: 12/@num;
            color: rgb(7,17,27);
            .icon-thumb_up,.icon-thumb_down{
              margin-right: 4/@num;
              line-height: 16/@num;
              font-size: 12/@num;
            }
            .icon-thumb_up{
              color: rgb(0,160,220);
            }
            .icon-thumb_down{
              color: rgb(147,153,159);
            }
          }
        }
        .no-rating{
          padding: 16/@num 0;
          font-size: 12/@num;
          color: rgb(147,153,159);
        }
      }
    }
  }
  .move-enter-active,.move-leave-active{
    transition: all .5s;
  }
  .move-enter,.move-leave-active{
    transform: translate3d(100%,0,0);
  }
</style>
