<template>
  <span class="icon" :class="[iconType,iconSize]"></span>
</template>

<script type="text/ecmascript-6">
 export default {
   props: {
     type: {
       type: Number
     },
     size: {
       type: Number
     },
     imgSize: {
       type: Number
     }
   },
   computed: {
     iconSize() {
       return 'icon-' + this.size;
     },
     iconType() {
       return this.classMap[this.type] + '_' + this.imgSize;
     }
   },
   created() {
     this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
   }
 };
</script>

<style lang="less" rel="stylesheet/less">
  @import "../../common/less/mixin";
  .icon{
    display: inline-block;
    vertical-align: top;
    margin-right: 4/@num;
    background-size: 12/@num 12/@num;
    background-repeat: no-repeat;
    &.icon-12{
      width: 12/@num;
      height: 12/@num;
      margin-right: 4/@num;
      background-size: 12/@num 12/@num;
    }
    &.icon-16{
      width: 16/@num;
      height: 16/@num;
      margin-right: 2/@num;
      background-size: 16/@num 16/@num;
    }
    &.icon-10{
      width: 12/@num;
      height: 12/@num;
      margin-right: 2/@num;
      background-size: 12/@num 12/@num;
    }
    &.decrease_1{
       .bg-image('decrease_1')
     }
    &.discount_1{
      .bg-image('discount_1')
    }
    &.guarantee_1{
      .bg-image('guarantee_1')
    }
    &.invoice_1{
      .bg-image('invoice_1')
    }
    &.special_1{
      .bg-image('special_1')
    }

    &.decrease_2{
      .bg-image('decrease_2')
    }
    &.discount_2{
      .bg-image('discount_2')
    }
    &.guarantee_2{
      .bg-image('guarantee_2')
    }
    &.invoice_2{
      .bg-image('invoice_2')
    }
    &.special_2{
      .bg-image('special_2')
    }

    &.decrease_3{
      .bg-image('decrease_3')
    }
    &.discount_3{
      .bg-image('discount_3')
    }
    &.guarantee_3{
      .bg-image('guarantee_3')
    }
    &.invoice_3{
      .bg-image('invoice_3')
    }
    &.special_3{
      .bg-image('special_3')
    }

    &.decrease_4{
      .bg-image('decrease_4')
    }
    &.discount_4{
      .bg-image('discount_4')
    }
    &.guarantee_4{
      .bg-image('guarantee_4')
    }
    &.invoice_4{
      .bg-image('invoice_4')
    }
    &.special_4{
      .bg-image('special_4')
    }
  }
</style>
