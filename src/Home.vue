<template>
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
             <el-button type="primary"  @click='UseAtlas' >Browse</el-button>
         </el-col >
     </el-row>
  </div>
</template>

<script>
export default {
    props:['G_URL'],
    data(){
        return {
            input_url : "http://127.0.0.1:8010",
        }
    },
    methods: {
        UseAtlas(){
            console.log(this.input_url);
            this.$emit('browse',this.input_url);
        },
    },
    mounted(){
        if( this.G_URL != '' )
            this.input_url = this.G_URL;
    },
};
</script>
