<template>
  <div id="app">
    <header>
    </header>
    <article>
        <component  :G_Atlas='G_Atlas' v-bind:is="selected"></component>
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

import Home from "./Home.vue"
import Viewer from "./Viewer.vue"

export default {
  data() {
    return {
      G_Atlas:'',
      selected:"Home",
    }
  },
  components:{
     Home,
     Viewer,
  },
  methods: {
      InitSummary(jsondata){
          console.log("save summary");
          this.G_Atlas['summary'] = jsondata;
          this.$nextTick(() => {
              console.log("start browser");
              this.selected = "Viewer";
          });
      },
  },
  mounted(){
    var uri = window.location.search.substring(1);
    var params = new URLSearchParams(uri);
    var baseurl=params.get("atlas");
    if( baseurl == null ) baseurl = ""
    //if( baseurl == null ) baseurl = "/test_data/"
    var atlas = {}
    atlas['main_url']    = baseurl;
    atlas['summary_url'] = baseurl + '/summary.json';
    atlas['gene_url']    = baseurl + '/gene.json';
    atlas['regulon_url'] = baseurl + '/regulon.json';
    atlas['ccc_dict_url'] = baseurl + '/ccc_dict.json'
    atlas['hotspot_url'] = baseurl + '/hotspot.json';
    atlas['meshes_url']  = baseurl + '/meshes.json';
    atlas['conf_url']    = baseurl + '/conf.json';
    atlas['paga_url']    = baseurl + '/paga.json';
    atlas['paga_line_url']  = baseurl + '/paga_line.json';
    atlas['anno_url']    = baseurl + '/Anno';
    atlas['genes_url']   = baseurl + '/Gene';
    atlas['regulons_url'] = baseurl + '/Regulon';
    atlas['hotspots_url'] = baseurl + '/Hotspot';
    atlas['ccc_url'] = baseurl + '/CCC';
    atlas['scoexp_url']  = baseurl + '/gene_scoexp';
    this.G_Atlas = atlas;
    var self = this;
    $.getJSON(atlas['summary_url'],function(_data) {
        console.log("summary loaded");
        self.InitSummary(_data);
    });
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
