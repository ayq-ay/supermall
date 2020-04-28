<template>
  <tab-bar>
      <tab-bar-item path="/home" >
        <img slot="item-img" src="~assets/img/tabbar/home.png" alt="">
        <img slot="item-img-active" src="~assets/img/tabbar/home-active.png" alt="" srcset="">
        <div slot="item-title">首页</div>
      </tab-bar-item>
      <tab-bar-item path="/category" >
        <img slot="item-img" src="~assets/img/tabbar/kind.png" alt="">
        <img slot="item-img-active" src="~assets/img/tabbar/kind-active.png" alt="" srcset="">
        <div slot="item-title">分类</div>
      </tab-bar-item>
      <tab-bar-item path="/cart" >
        <img slot="item-img" src="~assets/img/tabbar/cart.png" alt="">
        <img slot="item-img-active" src="~assets/img/tabbar/cart-active.png" alt="" srcset="">
        <div slot="item-title">购物车</div>
      </tab-bar-item>
      <tab-bar-item path="/my" >
        <img slot="item-img" src="~assets/img/tabbar/my.png" alt="">
        <img slot="item-img-active" src="~assets/img/tabbar/my-active.png" alt="" srcset="">
        <div slot="item-title">我的</div>
      </tab-bar-item>
    </tab-bar>
</template>

<script>
import Tabbar from '../../common/tabbar/Tabbar'
import TabbarItem from '../../common/tabbar/TabbarItem'

export default {
  name: 'MainTabBar',
  components: {
    'tab-bar': Tabbar,
    'tab-bar-item': TabbarItem
  }
}
</script>

<style>

</style>
