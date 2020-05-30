<template>
  <nav-bar>
    <div slot="left" class="back" @click="backClick">
      <img src="~assets/img/common/back.svg" alt="">
    </div>
    <div slot="center" class="titles">
      <div v-for="(it, i) in titles" :key="it.id" 
      class="title-item" 
      :class="{active: i === currentIndex}"
      @click="titleClick(i)">
        {{it}}
      </div>
    </div>
  </nav-bar>
</template>

<script>
import NavBar from 'components/common/navbar/NavBar'

export default {
  name: 'DetailNavBar',
  components: {
    NavBar
  },
  data(){
    return{
      titles: ['商品', '参数', '评论' , '推荐'],
      currentIndex: 0
    }
  },
  methods: {
    titleClick(i){
      this.currentIndex = i;
      this.$emit('titleClick', i)
    },
    backClick(){
      this.$router.back()
    }
  }
}
</script>

<style scoped>
.titles{
  display: flex;
  font-size: 13px;
}
.title-item{
  flex: 1;
}
.active{
  color: var(--color-high-text);
}
.back{
  padding-left: 12px;
}
.back img{
  margin-top: 12px;
}
</style>
