<template>
  <div class="tab-control">
    <div v-for="(it, i) in titles" :key="it.id" class="tab-control-item" :class="{active: currentIndex === i}"
    @click="itemClick(i)">
      <span>
        {{it}}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TabControl',
  props: {
    titles: {
      type: Array,
      default(){
        return []
      }
    }
  },
  data(){
    return{
      currentIndex: 0
    }
  },
  methods: {
    itemClick(i){
      this.currentIndex = i;
      this.$emit('tabClick', i);
    }
  }
}
</script>

<style scoped>
  .tab-control{
    width: 100%;
    display: flex;
    text-align: center;
    height: 40px;
    line-height: 40px;
  }
  .tab-control-item{
    font-size: 15px;
    flex: 1;
  }
  .tab-control-item span{
    padding: 5px;
  }
  .active{
    color: var(--color-high-text);
  }
  .active span{
    border-bottom: 3px solid var(--color-tint)
  }
</style>
