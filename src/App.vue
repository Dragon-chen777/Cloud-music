<template>
  <div id="app">
    <tab-bar class="tab-bar"></tab-bar>
    <left-menu />
    <Main />
    <play-music class="play-music" />
  </div>
</template>

<script>
import TabBar from 'components/content/tabbar/TabBar'
import LeftMenu from 'components/content/LeftMenu'
import Main from 'components/content/Main'
import PlayMusic from "components/content/playmusic/PlayMusic"
export default {
  name:'App',
  components:{
    TabBar,
    LeftMenu,
    Main,
    PlayMusic,
  }
}
</script>
<style>
@import url('./assets/css/base.css');
@import url('./assets/css/play.css');
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 100%;
  height: 100vh;
  overflow: hidden;
  user-select:none;
  background: #16181c;
  color: #fff;
}
</style>
