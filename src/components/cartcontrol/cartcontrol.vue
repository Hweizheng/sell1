<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart($event)">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>

    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart($event)"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';
  export default {
    props: {
      food: {
        type: Object
      }
    },
    created() {

    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
          // this.food.count = 1;
        } else {
          this.food.count++;
        }
        this.$emit('add', event.target);
      },
      decreaseCart(event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count) {
          this.food.count--;
        }
      }
    }
  };
</script>

<style lang="less" rel="stylesheet/less">
  @import "../../common/less/mixin";
  .cartcontrol{
    font-size: 0;
    .cart-decrease,.cart-add{
      display: inline-block;
      padding: 6/@num;
      transition: all .2s linear;
      .inner{
        display: inline-block;
        line-height: 24/@num;
        font-size: 24/@num;
        color: rgb(0,160,220);
        transition: all .3s linear;
        transform: rotate(0);
      }
      &.move-enter{
        opacity: 1;
        transform: translate3d(0,0,0);
        .inner{
          transform:rotate(0);
        }
      }
      &.move-leave{
        opacity: 0;
        transform: translate3d(24/@num,0,0);
        .inner{
          transform:rotate(180deg);
        }
      }
      &.move-enter-active{
        opacity: 0;
        transform: translate3d(24/@num,0,0);
        .inner{
          transform:rotate(180deg);
        }
      }
      &.move-leave-active{
        opacity: 0;
        transform: translate3d(24/@num,0,0);
        .inner{
          transform:rotate(180deg);
        }
      }
    }
    .cart-count{
      display: inline-block;
      vertical-align: top;
      width: 12/@num;
      padding-top: 6/@num;
      line-height: 24/@num;
      text-align: center;
      font-size: 10/@num;
      color: rgb(147,153,159);
    }
    .cart-add{
      display: inline-block;
      padding: 6/@num;
      line-height: 24/@num;
      font-size: 24/@num;
      color: rgb(0,160,220);
    }
  }

</style>
