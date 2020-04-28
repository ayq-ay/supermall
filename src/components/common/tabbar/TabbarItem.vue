<template>
  <div class="tabbar-item" @click='itemClick'>
    <div v-if='!isActive'>
      <slot name="item-img"></slot>
    </div>
    <div v-else >
      <slot name="item-img-active"></slot>
    </div>
    <div :style="activeStyle">
      <slot name="item-title"></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TabbarItem',
  props: {
    path: String,
    activeColor: {
      type: String,
      default: '#f00'
    }
  },
  computed: {
    isActive(){
      return this.$route.path.indexOf(this.path) !== -1
    },
    activeStyle(){
      return this.isActive ? {color: this.activeColor} :''
    }
  },
  methods: {
    itemClick(){
      this.$router.replace(this.path);
    }
  }
}
</script>

<style>
.tabbar-item{
  height: 49px;
  text-align: center;
  flex: 1;
  font-size: 14px;
}
.tabbar-item img{
  width: 24px;
  height: 24px;
  margin-top: 3px;
  margin-bottom: 2px;
  vertical-align: middle;
}
</style>
