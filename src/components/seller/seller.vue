<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc">
          <star :size="36" :score="seller.score"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span class="icon-favorite" :class="{'active':favorite}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supports">
          <!--eslint-disable-next-line-->
          <li class="support-item" v-for="item in seller.supports">
            <icon :type="item.type" :size="16" :imgSize="4"></icon>
            <!--<span class="icon" :class="classMap[item.type]"></span>-->
            <span class="text">{{item.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <!--eslint-disable-next-line-->
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" alt="">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title">商家信息</h1>
        <ul>
          <!--eslint-disable-next-line-->
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import icon from 'components/icon/icon';
  import star from 'components/star/star';
  import split from 'components/split/split';
  import BScroll from 'better-scroll';
  import {saveToLocal, loadFromLocal} from 'common/js/store';
  const ERR_OK = 0;
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return loadFromLocal(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        if (this.favorite) {
          return '已收藏';
        } else {
          return '收藏';
        }
      }
    },
    watch: {
      'seller'() {
        this.$nextTick(() => {
          this._initScroll();
          this._initPics();
        });
      }
    },
    mounted() {
      this.$nextTick(() => {
        this._initScroll();
        this._initPics();
      });
    },
    methods: {
      toggleFavorite(event) {
        if (!event._constructed) {
          return;
        }
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id, 'favorite', this.favorite);
      },
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.seller, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$refs.picList.style.width = width / 37.5 + 'rem';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$refs.picWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
      }
    },
    components: {
      star,
      split,
      icon
    }
  };
</script>

<style lang="less" rel="stylesheet/less">
  @import '../../common/less/mixin';
  .seller{
    position: absolute;
    top: 174/@num;
    bottom: 0;
    left: 0;
    width: 100%;
    overflow: hidden;
    .overview{
      padding: 18/@num;
      .title{
        font-size: 14/@num;
        color: rgb(7,17,27);
        margin-bottom: 8/@num;
        line-height: 14/@num;
      }
      .desc{
        padding-bottom: 18/@num;
        font-size: 0;
        .border-1px(rgba(7,17,27,.1));
        .star{
          display: inline-block;
          vertical-align: top;
          margin-right: 8/@num;
        }
        .text{
          display: inline-block;
          margin-right: 12/@num;
          line-height: 18/@num;
          vertical-align: top;
          color: rgb(77,85,93);
          font-size: 10/@num;
        }
      }
      .remark{
        display: flex;
        padding-top: 18/@num;
        .block{
          flex: 1;
          text-align: center;
          border-right: 1px solid rgba(7,17,27,.1);
          &:last-child{
            border: 0;
          }
          h2{
            margin-bottom: 4/@num;
            line-height: 10/@num;
            font-size: 10/@num;
            color: rgb(147,153,159);
          }
          .content{
            line-height: 24/@num;
            font-size: 10/@num;
            color: rgb(7,17,27);
            .stress{
              font-size: 24/@num;
            }
          }
        }
      }
      .favorite{
        position: absolute;
        width: 50/@num;
        top: 18/@num;
        right: 11/@num;
        text-align: center;
        font-size: 0;
        .icon-favorite{
          font-size: 24/@num;
          line-height: 24/@num;
          display: block;
          margin-bottom: 4/@num;
          color: #d4d6d9;
          &.active{
            color: rgb(240,20,20);
          }
        }
        .text{
          line-height: 10/@num;
          font-size: 10/@num;
          color: rgb(77,85,93);
        }
      }
    }
    .bulletin{
      padding: 18/@num 18/@num 0;
      .title{
        margin-bottom: 8/@num;
        line-height: 14/@num;
        color: rgb(7,17,27);
        font-size: 14/@num;
      }
      .content-wrapper{
        padding: 0 12/@num 16/@num;
        .border-1px(rgba(7,17,27,.1));
        .content{
          line-height: 24/@num;
          font-size: 12/@num;
          color: rgb(240,20,20);
        }
      }
      .supports{
        .support-item{
          padding: 16/@num 12/@num;
          font-size: 0;
          .border-1px(rgba(7,17,27,.1));
          &:last-child:after{
            border: none;
          }
        }
        .text{
          line-height: 16/@num;
          font-size: 12/@num;
          color: rgb(7,17,27);
        }
      }
    }
    .pics{
      padding: 18/@num;
      .title{
        margin-bottom: 12/@num;
        line-height: 14/@num;
        color: rgb(7,17,27);
        font-size: 14/@num;
      }
      .pic-wrapper{
        width: 100%;
        overflow: hidden;
        white-space: nowrap;
        .pic-list{
          font-size: 0;
          .pic-item{
            display: inline-block;
            margin-right: 6/@num;
            width: 120/@num;
            height: 90/@num;
            img{
              width: 100%;
              height: 100%;
            }
            &:last-child{
              margin-right: 0;
            }
          }
        }
      }
    }
    .info{
      padding: 18/@num 18/@num 0;
      color: rgb(7,17,27);
      .title{
        padding-bottom: 12/@num;
        line-height: 14/@num;
        font-size: 14/@num;
        color: rgb(7,17,27);
        .border-1px(rgba(7,17,27,.1));
      }
      .info-item{
        padding: 16/@num 12/@num;
        line-height: 16/@num;
        font-size: 16/@num;
        .border-1px(rgba(7,17,27,.1));
        &:last-child:after{
          border: none;
        }
      }
    }
  }
</style>
