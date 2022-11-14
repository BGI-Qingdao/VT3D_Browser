<template>
  <div id="app">
    <header>
      <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect" active-text-color="#409eff" style="margin: 0px;">
        <el-menu-item index="1">Configure</el-menu-item>
        <el-menu-item index="2" disabled >Browser</el-menu-item>
      </el-menu>
    </header>
    <article>
        <component :G_Atlas='G_Atlas' @browse='BrowseURL' v-bind:is="selected"></component>
    </article>
    <footer>
      <hr>
      <p>Any page issue or bug resport, please contact: guolidong@genomics.cn; liyao1@genomics.cn; </p>
      <p> Powered by Element.js framework and Echarts.js library.</p>
    </footer>
  </div>
</template>

<script>
import Vue from 'vue'
import Element from 'element-ui'
import locale from 'element-ui/lib/locale/lang/en'
Vue.use(Element, { locale })
import 'element-theme-dark';

import Home from "./Home.vue";
import Viewer from "./Viewer.vue"

export default {
  data() {
    return {
      G_Atlas:'',
      activeIndex: '1',
      selected:"Home"
    }
  },
  components:{
     Home,
     Viewer,
  },
  methods: {
    handleSelect(key, keyPath) {
        this.activeIndex = key;
        if ( key == "1")
        {
            this.selected = "Home";
        }
        else if ( key == "2")
        {
            this.selected = "Viewer";
        }
        else
        {
            this.selected = "Home";
        }
    },
    BrowseURL(atlas){
        this.G_Atlas = atlas;
        this.$nextTick(() => {
            this.handleSelect("2",["2"]);
        });
    },
  },
}
</script>

<style>
#app {
  font-family: Arial;
  text-align: center;
  margin-top: -8px;
}
hr{
    border:2px solid #ccc;
}
header{
    position: sticky;
    z-index: 999;
    top: 0;
    margin-bottom: 8px;
}
</style>
