<template>
  <div class='tabbar'>
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'Tabbar'
}
</script>

<style>
.tabbar{
  background-color: #f6f6f6;
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  box-shadow: 0px -2px 3px rgba(100, 100, 100, .1);
  display: flex;
}
</style>
