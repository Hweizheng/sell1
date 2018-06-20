<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @select="selectRating" @toggle="toggleContent" :select-type="selectType" :only-content="onlyContent"  :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <!--eslint-disable-next-line-->
          <li class="rating-item" v-for="rating in ratings" v-show="needShow(rating.rateType,rating.text)">
            <div class="avatar">
              <img :src="rating.avatar" alt="">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <!--eslint-disable-next-line-->
                <span v-for="item in rating.recommend" class="item">{{item}}</span>
              </div>
              <div class="time">
                {{rating.rateTime|formatDate}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {formatDate} from '../../common/js/date';
  import star from 'components/star/star';
  import split from 'components/split/split';
  import ratingselect from 'components/ratingselect/ratingselect';

  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;
  const ERR_OK = 0;
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true
      };
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    created() {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.ratings = response.data;
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratings, {
              click: true
            });
          });
        }
      });
    },
    methods: {
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
      star,
      split,
      ratingselect
    }
  };
</script>

<style lang="less" rel="stylesheet/less">
  @import "../../common/less/mixin.less";
  .ratings{
    position: absolute;
    top: 174/@num;
    bottom: 0;
    left: 0;
    width: 100%;
    overflow: hidden;
    .overview{
      display: flex;
      padding: 18/@num 0;
      .overview-left{
        flex: 0 0 137/@num;
        padding: 6/@num 0;
        width: 137/@num;
        border-right: 1px solid rgba(7,17,27,.1);
        text-align: center;
        .score{
          margin-bottom: 6/@num;
          line-height: 28/@num;
          font-size: 24/@num;
          color: rgb(255,153,0);
        }
        .title{
          margin-bottom: 8/@num;
          line-height: 12/@num;
          font-size: 12/@num;
          color: rgb(7,17,27);
        }
        .rank{
          line-height: 10/@num;
          font-size: 10/@num;
          color: rgb(147,153,159);
        }
      }
      .overview-right{
        flex: 1;
        padding: 6/@num 0 6/@num 24/@num;
        .score-wrapper{
          margin-bottom: 8/@num;
          font-size: 0;
          .title{
            display: inline-block;
            vertical-align: top;
            font-size: 12/@num;
            color: rgb(7,17,27);
            line-height: 18/@num;
          }
          .star{
            display: inline-block;
            vertical-align: top;
            margin: 0 12/@num;
          }
          .score{
            display: inline-block;
            vertical-align: top;
            font-size: 12/@num;
            color: rgb(255,153,0);
            line-height: 18/@num;
          }
        }
        .delivery-wrapper{
          font-size: 0;
          .title{
            line-height: 18/@num;
            font-size: 12/@num;
            color: rgb(7,17,27);
          }
          .delivery{
            font-size: 12/@num;
            margin-left: 12/@num;
            color: rgb(147,153,159);
          }
        }
      }
    }
    .rating-wrapper{
      padding: 0 18/@num;
      .rating-item{
        display: flex;
        padding: 18/@num 0;
        .border-1px(rgba(7,17,27,.1));
        .avatar{
          flex: 0 0 28/@num;
          width: 28/@num;
          margin-right: 12/@num;
          img{
            border-radius: 50%;
            width: 28/@num;
            height: 28/@num;
          }
        }
        .content{
          position: relative;
          flex: 1;
          .name{
            margin-bottom: 4/@num;
            line-height: 12/@num;
            font-size: 10/@num;
            color: rgb(7,17,27);
          }
          .star-wrapper{
            font-size: 0;
            margin-bottom: 6/@num;
            .star{
              display: inline-block;
              vertical-align: top;
              margin-right: 6/@num;
            }
            .delivery{
              display: inline-block;
              vertical-align: top;
              font-size: 10/@num;
              color: rgb(147,153,159);
            }
          }
          .text{
            line-height: 18/@num;
            color: rgb(7,17,27);
            font-size: 12/@num;
            margin-bottom: 8/@num;
          }
          .recommend{
            line-height: 16/@num;
            font-size: 0;
            .icon-thumb_up,.item{
              display: inline-block;
              margin: 0 8/@num 4/@num 0;
              font-size: 9/@num;
            }
            .icon-thumb_up{
              color: rgb(0,160,220);
            }
            .item{
              padding: 0 6/@num;
              border: 1px solid rgba(7,17,27,.1);
              color: rgb(147,153,159);
              border-radius: 1px;
              background: #fff;
            }
          }
          .time{
            position: absolute;
            top: 0;
            right: 0;
            line-height: 12/@num;
            color: rgb(147,153,159);
            font-size: 10/@num;
          }
        }
      }
    }
  }
</style>
