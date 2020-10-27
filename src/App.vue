<template>
  <div id="app">
    <!-- <img src="./assets/logo.png"> -->
    <left-nav></left-nav>
    <div class="main">
        <router-view></router-view>
    </div>
  </div>
</template>

<script>
//引入左侧边栏
import leftNav from '@/components/common/leftNav';
export default {
  name: 'App',
  components: {
      leftNav,
  }
}
</script>

<style>
#app {
  font-family: 'Microsoft YaHei ','Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100%;
  /* margin-top: 60px; */
}
.main{
    float: left;
    width: 95%;
    background-color: #eff2f7;
    height: 100%;
    overflow: auto;
}
</style>
