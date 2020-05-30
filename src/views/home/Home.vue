<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">
        购物街
      </div>
    </nav-bar>
    <tab-control
      @tabClick="tabClick"
      :titles="['流行' ,'新款', '精选']"
      ref="tabControl1" 
      :class="{fixed: isTabFixed}"
      class="tab-control"
      v-show="isTabFixed"/>
    <scroll class="content"
            ref="scroll" 
            :probe-type="3" 
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore"
            >
      <home-swiper :banners="banners" @swiperImageLoad="swiperImageLoad"/>
      <home-recommend-view :recommends="recommends"/>
      <home-feature />
      <tab-control
      @tabClick="tabClick"
      :titles="['流行' ,'新款', '精选']"
      ref="tabControl2" 
      :class="{fixed: isTabFixed}"/>
      <goods-list :goods="showGoods" />
    </scroll>
    <!-- 在我们需要监听一个组件的原生事件时，必须给对应的事件加上.native修饰符才能进行监听 -->
    <back-top @click.native="backClick" v-show="isShowBackTop" />
    
  </div>
</template>

<script>
//页面组件
  import HomeSwiper from './childComps/HomeSwiper'
  import HomeRecommendView from './childComps/HomeRecommendView'
  import HomeFeature from './childComps/HomeFeature'

  //公共组件
  import NavBar from 'components/common/navbar/NavBar'
  import TabControl from 'components/content/tabControl/TabControl'
  import GoodsList from 'components/content/goods/GoodsList'
  import Scroll from 'components/common/scroll/Scroll'
  import BackTop from 'components/content/backTop/BackTop'
  import {itemListenerMixin} from 'common/mixin'

  import {
    getHomeMultidata,
    getHomeGoods
    } from "network/home"

  import {debounce} from '@/common/utils'

  export default {
    name: 'Home',
    components: {
      NavBar,
      HomeSwiper,
      HomeRecommendView,
      HomeFeature,
      TabControl,
      GoodsList,
      Scroll,
      BackTop
    },
    mixins: [],
    data(){
      return{
        banners: [],
        recommends: [],
        goods: {
          'pop' : {page: 0, list: []},
          'new' : {page: 0, list: []},
          'sell' : {page: 0, list: []}
        },
        currentType: 'pop',
        isShowBackTop: false,
        tabOffsetTop: 0,
        isTabFixed: false,
        saveY: 0
      }
    },
    computed: {
      showGoods(){
        return this.goods[this.currentType].list
      }
    },
    destroyed () {
      console.log('home destroyed');
    },
    deactivated () {//非活跃时
      //1.保存Y值
      this.saveY = this.$refs.scroll.getScrollY()
      console.log(this.saveY)

      //2.取消全局事件监听
      this.$bus.$off('itemImgLoad', this.itemImgListener)
    },
    activated () {//活跃时
      this.$refs.scroll.refresh()
      this.$refs.scroll.scrollTo(0, this.saveY, 0);
    },
    created () {
      //1.请求多个数据
      this.getHomeMultidata()
      //2.请求商品数据
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')

    },
    mounted () {
      /* //1.图片加载完成的事件监听
      //const refresh = this.debounce(this.$refs.scroll.refresh, 500)
      const refresh = debounce(this.$refs.scroll.refresh, 200)

      //对监听的事件进行保存
      this.itemImgListener = () => {
        refresh()
      }
      this.$bus.$on('itemImageLoad', this.itemImgListener)  */
    },
    methods: {
      /* 
        事件监听相关的方法
      */
      
      tabClick(i){
        this.$refs.scroll.refresh()
        switch (i){
          case 0:
            this.currentType = 'pop'
            break
          case 1:
            this.currentType = 'new'
            break
          case 2:
            this.currentType = 'sell'
            break
        }
        this.$refs.tabControl1.currentIndex = i;
        this.$refs.tabControl2.currentIndex = i;
        
      },
      backClick(){
        this.$refs.scroll.scrollTo(0, 0, 500)
      },
      contentScroll(position){
        //1.判断BackTop是否显示
        this.isShowBackTop = -position.y > 1000

        //2.决定tabControl是否吸顶(position: fixed)
        this.isTabFixed = (-position.y > this.tabOffsetTop)
      },
      loadMore(){
        this.getHomeGoods(this.currentType)
      },
      swiperImageLoad(){
        /* console.log(this.$refs.tabControl.$el.offsetTop); */ 
      //2.获取tabControl的offsetTop
      //所有的组件都有一个属性$el:用于获取组建中的元素
        this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop
        //console.log(this.$refs.tabControl2.$el.offsetTop)
      },
      /* 
        网络请求相关
      
      */
      getHomeMultidata(){
        getHomeMultidata().then(res =>{
          this.banners = res.data.banner.list;
          this.recommends = res.data.recommend.list;
        })
      },
      getHomeGoods(type){
        const page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list);
          this.goods[type].page += 1;
          
          //完成上拉加载
          this.$refs.scroll.finishPullUp()
        })
      }
    }
  }
</script>

<style scoped>
  #home{
    height: 100vh;
    /* 
    vh ->viewport height
    100vh代表100%视图高度
     */
     position: relative;
     /* padding-top: 44px; */
  }
  .home-nav{
    width: 100%;
    background-color: var(--color-tint);
    color: #fff;

    /* 在使用浏览器原生滚动时，为了让导航不跟随一起滚动方使用以下属性 */
    position: fixed;
    top: 0;
    left: 0;
    z-index: 9;
  }

  .tab-control{
    position: sticky;
    top: 44px;
    background-color: var(--color-background);
    z-index: 9;
  }
  .fixed{
    position: fixed;
    top: 44px;
    left: 0;
    right: 0;
  }
  .content{
    /* height: calc(100% - 93px);
    margin-top: 44px; */
    position: absolute;
    top: 44px;
    left: 0;
    right: 0;
    bottom: 49px;
  }
</style>
