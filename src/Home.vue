<template>
<div>
  <div>
     <el-row>
         <el-col :span="24" >
             <p>Please type the address of VT3D atlas</p>
         </el-col >
     </el-row>
     <el-row>
         <el-col :span="4" >
             <p> Atlas:</p>
         </el-col >
         <el-col :span="16" >
             <el-input placeholder="http://127.0.0.1:8010" v-model="input_url"></el-input>
         </el-col >
         <el-col :span="4" >
             <el-button type="primary"  @click='ConnectAtlas' >Connect</el-button>
         </el-col >
     </el-row>
  </div>
  <div v-show="summary_div" style="background-color: #777">
     <el-row>
         <el-col :span="4" >
             <p> Summary:</p>
         </el-col >
         <el-col :span="16" >
              <p> Total number of cells: {{num_of_cells}}. Total number of genes: {{num_of_genes}}. </p>
         </el-col>
         <el-col :span="4" >
             <el-button type="success"  @click='BrowseAtlas' >Browse</el-button>
         </el-col>
     </el-row>
  </div>
</div>
</template>

<script>
export default {
    props: ['G_Atlas'],
    data() {
        return {
            input_url : "http://127.0.0.1:8010",
            summary_div : false,
            num_of_cells: 0,
            num_of_genes: 0,
            atlas : {}
        }
    },
    methods: {
        InitAtlas(){
            this.atlas = {}
            this.atlas['main_url'] = this.input_url;
            this.atlas['summary_url'] = this.input_url + '/summary.json';
            this.atlas['gene_url'] = this.input_url + '/gene.json';
            this.atlas['anno_url'] = this.input_url + '/Anno';
            this.atlas['gene_url'] = this.input_url + '/Gene';
            this.atlas['mesh_url'] = this.input_url + '/Mesh';
        },
        InitSummary(jsondata){
            this.atlas['summary'] = jsondata;
            this.num_of_cells = jsondata['total_cell'];
            this.num_of_genes = jsondata['total_gene'];
            this.summary_div = true;
        },
        ConnectAtlas() {
            this.InitAtlas()
            var self = this;
            $.getJSON(this.atlas['summary_url'],function(_data) {
                console.log("summary loaded");
                self.InitSummary(_data);
            });
        },
        BrowseAtlas() {
            console.log(this.input_url);
            this.$emit('browse',this.input_url);
        },
    },
    mounted() {
    },
};
</script>
