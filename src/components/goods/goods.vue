<template>
  <div>
    <div class="goods">
      <div class="menu-wrapper" ref="menuWrapper" >
        <ul>
          <!--eslint-disable-next-line-->
          <li v-for="(item,index) in goods" class="menu-item" :class="{'active' : currentIndex === index }" @click="selectMenu(index,$event)">
          <span class="text">
            <icon :type="item.type" v-if="item.type>0" :size="10" :imgSize="3"></icon>
            <!--<span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>-->
            {{item.name}}
          </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foodsWrapper" >
        <ul>
          <!--eslint-disable-next-line-->
          <li v-for="item in goods" class="food-list food-list-hook">
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <!--eslint-disable-next-line-->
              <li  @click.stop.prevent="selectFood(food,$event)" v-for="food in item.foods" class="food-item">
                <div class="icon">
                  <img :src="food.icon" alt="">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">¥{{food.price}}</span><span v-show="food.oldPrice" class="old">¥{{food.oldPrice}}</span>
                  </div>
                  <div class="cartcontrol-wrapper">
                    <cartcontrol @add="addFood" :food="food"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopcart ref="shopcart" :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
    <food :food="selectedFood" ref="foodControl" @add="addFood"></food>
  </div>
</template>

<script type="text/ecmascript-6">
  import icon from 'components/icon/icon';
  import BScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import food from 'components/food/food';
  const ERR_OK = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      };
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
      },
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    created() {
      this.$http.get('/api/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          // console.log(this.goods);
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
            // this._drop();
          });
        }
      });
    },
    components: {
      icon,
      shopcart,
      cartcontrol,
      food
    },
    methods: {
      selectFood(food, event) {
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food;
        this.$refs.foodControl.show();
      },
      selectMenu(index, event) {
        if (!event._constructed) {
          return;
        }
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        // console.log(index);
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      addFood(target) {
        this._drop(target);
      },
      _drop(target) {
        // 体验优化,异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        });
      },
      _initScroll() {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight() {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      }
    }
  };
</script>

<style  lang="less" rel="stylesheet/less">
  @import "../../common/less/mixin";
  .goods{
    display: flex;
    position: absolute;
    width: 100%;
    top: 174/@num;
    bottom:46/@num;
    overflow: hidden;
    .menu-wrapper{
      flex: 0 0 80/@num;
      width: 80/@num;
      background: #f3f5f7;
      .menu-item{
        display: table;
        height: 54/@num;
        width: 56/@num;
        padding: 0 12/@num;
        line-height: 14/@num;
        &.active{
          position: relative;
          z-index: 10;
          margin-top: -1/@num;
          background: #fff;
          font-weight: 700;
          .text{
            .boder-none();
          }
        }
        .text{
          display: table-cell;
          width: 56/@num;
          vertical-align: middle;
          .border-1px(rgba(7,17,27,.1));
          font-size: 12/@num;
        }
      }
    }
    .foods-wrapper{
      flex: 1;
      background: #fff;
      .title{
        padding-left: 14/@num;
        height: 26/@num;
        line-height: 26/@num;
        border-left: 2px solid #d9dde1;
        font-size: 12/@num;
        color: rgb(147,153,159);
        background: #f3f5f7;
      }
      .food-item{
        display: flex;
        margin: 18/@num;
        padding-bottom: 18/@num;
        .border-1px(rgba(7,17,27,.1));
        &:last-child{
          .boder-none();
          margin-bottom: 0;
        }
        .icon{
          flex: 0 0 57/@num;
          margin-right: 10/@num;
          img{
            width: 57/@num;
            height: 57/@num;
          }
        }
        .content{
          flex: 1;
          .name{
            margin: 2/@num 0 8/@num 0;
            height: 14/@num;
            line-height: 14/@num;
            font-size: 14/@num;
            color: rgb(7,17,27);
          }
          .desc,.extra{
            line-height: 10/@num;
            font-size: 10/@num;
            color: rgb(147,153,159);
          }
          .desc{
            margin-bottom: 8/@num;
            line-height: 12/@num;
          }
          .extra{
            .count{
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
          .cartcontrol-wrapper{
            position: absolute;
            right: 0;
            bottom: 12/@num;
          }
        }
      }
    }
  }

</style>
