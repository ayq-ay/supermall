<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" ref="nav" @titleClick="titleClick"/>
    <scroll class="content"
            ref="scroll"
            @scroll="contentScroll"
            :probe-type="3">
      <!-- 属性：topImages 传入值：top-images -->
      <detail-swiper :topImages="topImages"  />
      <detail-base-info :goods="goods" />
      <detail-shop-info :shop="shop" />
      <detail-goods-info :detailInfo="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info ref="params" :paramInfo="paramInfo" />
      <detail-comment-info ref="comment" :commentInfo="commentInfo" />
      <goods-list ref="recommend" :goods="recommends"/>
    </scroll>
  </div>
</template>

<script>
import DetailNavBar from './childComps/DetailNavBar'
import DetailSwiper from './childComps/DetailSwiper'
import DetailBaseInfo from './childComps/DetailBaseInfo'
import DetailShopInfo from './childComps/DetailShopInfo'
import DetailGoodsInfo from './childComps/DetailGoodsInfo'
import DetailParamInfo from './childComps/DetailParamInfo'
import DetailCommentInfo from './childComps/DetailCommentInfo'

import Scroll from 'components/common/scroll/Scroll'
import GoodsList from 'components/content/goods/GoodsList'

import {getDetail, Goods, Shop, GoodsParam, getRecommend} from 'network/detail'
import {debounce} from 'common/utils'
import {itemListenerMixin} from 'common/mixin'

export default {
  name: 'Detail',
  mixins: [itemListenerMixin],
  data(){
    return{
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      themeTopYs: [],
      getThemeTopY: null,
      currentIndex: 0
    }
  },
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    Scroll,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    GoodsList
  },
  created () {
    //1.保存传入的iid
    this.iid = this.$route.params.iid

    //2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      //1.获取数据
      const data = res.result

      //2.获取顶部的图片轮播数据
      this.topImages = data.itemInfo.topImages

      //3.获取商品信息
      this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)

      //4.创建店铺信息的对象
      this.shop = new Shop(data.shopInfo)

      //5.获取商品的详情数据
      this.detailInfo = data.detailInfo

      //6.获取参数信息
      this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)

      //7.取出评论信息
      if(data.rate.cRate !== 0){
        this.commentInfo = data.rate.list[0];
      }

      /* //1.第一次获取，值不对
      //原因：this.$refs.params.$el压根没有渲染
      this.themeTopYs = [];

      this.themeTopYs.push(0);
      this.themeTopYs.push(this.$refs.params.$el.offsetTop);
      this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
      this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);

      console.log(this.themeTopYs)

      this.$nextTick( () => {
        //2.第二次获取：值不对
        //原因：图片没有计算在内
        //更具最新的数据，对应的DOM是已经被渲染出来
        //但是图片依然是没有加载完（目前获取到的offsetTop不包含其中的图片）
        //offsetTop值不对的时候，都是因为图片的原因
        this.themeTopYs = [];

        this.themeTopYs.push(0);
        this.themeTopYs.push(this.$refs.params.$el.offsetTop);
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);

        console.log(this.themeTopYs)
      }) */
    })

    //3.请求推荐数据
    getRecommend().then(res => {
      this.recommends = res.data.list
    })

    //4.给getThemeTopY赋值(对给this.themeTopYs赋值的操作进行防抖)
    this.getThemeTopY = debounce(() => {
      this.themeTopYs = [];
      this.themeTopYs.push(0);
      this.themeTopYs.push(this.$refs.params.$el.offsetTop);
      this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
      this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);

      console.log(this.themeTopYs)
    })
  },
  methods: {
    imageLoad(){
      this.$refs.scroll.refresh();
      this.getThemeTopY()
    },
    titleClick(i){
      this.$refs.scroll.scrollTo(0, -this.themeTopYs[i] + 44, 200)
    },
    contentScroll(position){
      //获取y值
      const positionY = -position.y

      //2.positionY和主题中值进行对比
      //[0, 1, 2, 3]
      //positionY在0和1之间，index = 0
      //positionY在1和2之间，index = 1
      //positionY在2和3之间，index = 2
      //positionY在大于等于3，index = 3
      let length = this.themeTopYs.length;
      for(let i = 0; i< length; i++){
        //let i in this.themeTopYs
        //这种写法的i是字符串，需要转换成数字
        /* if(positionY > this.themeTopYs[i] && positionY < this.themeTopYs[i*1+1]){

        } */
        let currentY = this.themeTopYs[i] - 45;
        let currentYB = this.themeTopYs[i+1] - 45;
        if(this.currentIndex !== i && ((i< length - 1 && positionY >= currentY && positionY < currentYB) || (i === length - 1 && positionY >= currentY))){
          this.currentIndex = i;
          /* console.log(currentIndex) */
          this.$refs.nav.currentIndex = this.currentIndex;
          //console.log(this.currentIndex)
        }
      }
    }
  },
  mounted () {
    
  },
  updated () {
    
  },
  destroyed () {
    this.$bus.$off('itemImgLoad', this.itemImgListener)
  } 
}
</script>

<style scoped>
  #detail{
    position: relative;
    z-index: 99;
    background-color: #fff;
    height: 100vh;
  }
  .content{
    height: calc(100% - 44px);
    /* padding-top: 44px;
    box-sizing: border-box; */
  }
  .detail-nav{
    position: relative;
    z-index: 9;
    background-color: #fff;
  }
</style>
