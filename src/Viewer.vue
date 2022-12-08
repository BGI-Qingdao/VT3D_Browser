<template>
<div>
    <!---------------------------- All floating items begin --------------------------------------------------- -->
    <div  style="height=1px;">
      <el-dialog title="celltype color" style="width:500px;" :visible.sync="drawer" direction="ltr" :before-close="OnDrawerClose">
          <div>
              <hr>
              <!-- start of color palette -->
              <div style='width:100%;height:400px;'>
                <sketch-picker align='center' v-model="color" @input="colorValueChange"></sketch-picker>
                <el-row style="margin-top:3px;margin-bottom:2px">
                    <el-col :span="12" >
                        <el-button type='success' @click='applyColor'>Apply</el-button>
                    </el-col>
                    <el-col :span="12" >
                        <el-button type='info' @click='closeColor'>Close</el-button>
                    </el-col>
                </el-row>
              </div>
              <!-- end of color palette -->
          </div> 
          <!-- end of dialog div -->
      </el-dialog>
      <el-dialog title="mesh color" style="width:500px;" :visible.sync="drawer_mesh" direction="ltr" :before-close="OnDrawerMeshClose">
          <div>
              <hr>
              <!-- start of color palette -->
              <div style='width:100%;height:400px;'>
                <sketch-picker align='center' v-model="color" @input="colorValueChange"></sketch-picker>
                <el-row style="margin-top:3px;margin-bottom:2px">
                    <el-col :span="12" >
                        <el-button type='success' @click='applyMeshColor'>Apply</el-button>
                    </el-col>
                    <el-col :span="12" >
                        <el-button type='info' @click='closeMeshColor'>Close</el-button>
                    </el-col>
                </el-row>
              </div>
              <!-- end of color palette -->
          </div>
          <!-- end of dialog div -->
      </el-dialog>
    </div>
    <!---------------------------- All floating items end --------------------------------------------------- -->
    <!---------------------------- Main windown begin --------------------------------------------------- -->
    <el-row>
      <el-button class="floatsetting" v-show="!display_setting" type="primary" icon="el-icon-setting" @click="span_setting" >{{setting_title}}</el-button>
      <!-- ----------The setting grid begin --------------------------------------------------------------- -->
      <el-col :span="setting_w" style="height:100%">
          <div class="grid-content bg-purple">
              <el-button  type="primary" icon="el-icon-setting" width='100%' @click="span_setting" >{{setting_title}}</el-button>
              <!-- ----------The setting panel begin --------------------------------------------------------------- -->
              <transition name="el-zoom-in-center"  >
                <div v-show="display_setting" class="transition-box" align='center' style="margin:3px; border: 3px solid #ffffff;"> 
                     <div align="center"  style="margin:3px;  border: 3px solid #ccc;" > 
                         <!-- ----------Choose mode begin --------------------------------------------------------------- -->
                         <el-row style="margin-top:3px;margin-bottom:2px">
                             <el-col :span="8" >
                                <span  class='mspan'>Mode:</span>
                             </el-col>
                             <el-col :span="16" >
                                 <el-select  v-model="curr_mode" placeholder="curr_mode" @change="OnChangeMode">
                                   <el-option
                                     v-for="item in all_modes"
                                     :key="item"
                                     :label="item"
                                     :value="item">
                                   </el-option>
                                 </el-select>
                             </el-col>
                         </el-row>
                         <!-- ----------Choose mode end --------------------------------------------------------------- -->
                     </div>
                     <!-- ----------The Baisc settings end -------------------------------------------------------------- -->
                  <el-collapse v-model='collapseName'>
                     <!-- ----------The Baisc settings begin --------------------------------------------------------------- -->
                      <el-collapse-item :title="mode_title" name="1" >
                      <!-- ----------mode setting begin --------------------------------------------------------------- -->
                         <!-- ----------cell type mode begin--------------------------------------------------------------- -->
                         <div align="center" v-show="is_ct_mode" style="margin:3px; border: 3px solid #ccc;">
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="8" >
                                   <span  class='mspan'>Annotation:</span>
                                </el-col>
                                <el-col :span="16" >
                                    <el-select  v-model="curr_anno" placeholder="curr_anno" @change="OnChangeAnno">
                                      <el-option
                                        v-for="item in anno_array"
                                        :key="item"
                                        :label="item"
                                        :value="item">
                                      </el-option>
                                    </el-select>
                                </el-col>
                            </el-row>
                            <!-- ----------cell type selection table begin --------------------------------------------------------------- -->
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <!-- 1. cell table content -->
                                <el-table class="table" ref="clusterTable" style="width:100%;" :show-header='false'
                                  :height='height' :row-key="getRowKey" :highlight-current-row='true'
                                  :data="tableDataClusters.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                                  @selection-change="handleSelectionChange"
                                  @row-click="getRowCelltype" >
                                    <el-table-column :reserve-selection="true" type="selection" ></el-table-column>
                                    <el-table-column property="Celltype" label="Cell Type" > </el-table-column>
                                    <el-table-column label="Display" >
                                      <el-button @click="showColorPalette">color</el-button>
                                    </el-table-column>
                                </el-table>
                                <el-pagination layout="total,sizes" 
                                  :total="this.tableDataClusters.length" 
                                  :page-size="pageSize"
                                  :page-sizes="[5,10,20,30]"
                                  @size-change="handleSizeChange" >
                                </el-pagination>
                                <el-pagination layout="prev, jumper, next"
                                  :total="this.tableDataClusters.length" 
                                  :page-size="pageSize" 
                                  :current-page.sync="currentPage">
                                </el-pagination>
                            </el-row>
                            <!-- 1. cell table content end-->
                            <div>
                               <!-- start of buttons -->
                               <hr class='dhr'>
                               <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="8" >
                                      <el-button type='success' @click="applyStatus">Apply</el-button>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-button type='warning' @click='clearSelect'>Clear</el-button>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-button type='primary' @click='resetSelect'>Reset</el-button>
                                  </el-col>
                               </el-row>
                               <!-- end of buttons -->
                            </div>
                            <!-- ----------cell type selection table end--------------------------------------------------------------- -->
                         </div>
                         <!-- ----------cell type mode end--------------------------------------------------------------- -->
                         <!-- ----------gene selection mode start--------------------------------------------------------------- -->
                         <div align="center"  v-show="is_ge_mode" style="margin:3px; border: 3px solid #ccc;">
                            <div align="left" style="margin-left:10px;">
                                <el-row style="margin-top:1px;margin-bottom:1px">
                                    <span class='mspan'>Please choose an Gene:</span>
                                </el-row>
                                <el-row style="margin-top:3px;margin-bottom:2px">
                                    <el-col :span="16" >
                                        <el-input  v-model="input_gene_id" placeholder=""></el-input>
                                    </el-col>
                                    <el-col :span="8" >
                                        <el-button type="success" @click.native="updateTable">Search</el-button>
                                    </el-col>
                                </el-row>
                                <el-row style="margin-top:3px;margin-bottom:2px">
                                    <el-col :span="24" >
                                        <!-- 2022-10-10 add gene table -->
                                        <el-table ref="geneTable" 
                                        :show-header='true' class="table"
                                        :data="tableData.slice((currentGenePage-1)*pageSize,currentGenePage*pageSize)"
                                        :highlight-current-row='true'
                                        stripe
                                        @row-dblclick='handleRow'>
                                            <el-table-column prop='smed' label='gene name'></el-table-column>
                                        </el-table>
                                        <!-- gene table end-->
                                    </el-col>
                                </el-row>
                                <el-row style="margin-top:3px;margin-bottom:2px">
                                        <el-pagination layout="total, prev,jumper,next"
                                            :total="this.tableData.length"
                                            :page-size="pageSize" 
                                            :current-page.sync="currentGenePage">
                                        </el-pagination>
                                </el-row>
                            </div>
                         </div>
                         <!-- ----------gene selection mode start--------------------------------------------------------------- -->
                         <!-- ----------digital in situ mode start--------------------------------------------------------------- -->
                         <div align="center"  v-show="is_gc_mode" style="margin:3px; border: 3px solid #ccc;">
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="6" >
                                   <span  class='mspan'>red:</span>
                                </el-col>
                                <el-col :span="12" >
                                    <el-input  v-model="input_gene_red" :placeholder="input_gene_red"></el-input>
                                </el-col>
                                <el-col :span="6" >
                                    <el-button type="success" @click.native="ApplyRed">set</el-button>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="6" >
                                   <span  class='mspan'>green:</span>
                                </el-col>
                                <el-col :span="12" >
                                    <el-input  v-model="input_gene_green" :placeholder="input_gene_green"></el-input>
                                </el-col>
                                <el-col :span="6" >
                                    <el-button type="success" @click.native="ApplyGreen">set</el-button>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="6" >
                                   <span  class='mspan'>blue:</span>
                                </el-col>
                                <el-col :span="12" >
                                    <el-input  v-model="input_gene_blue" :placeholder="input_gene_blue"></el-input>
                                </el-col>
                                <el-col :span="6" >
                                    <el-button type="success" @click.native="ApplyBlue">set</el-button>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="6" >
                                   <span  class='mspan'>gray:</span>
                                </el-col>
                                <el-col :span="12" >
                                    <el-input  v-model="input_gene_gray" :placeholder="input_gene_gray"></el-input>
                                </el-col>
                                <el-col :span="6" >
                                    <el-button type="success" @click.native="ApplyGray">set</el-button>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="6" >
                                   <span  class='mspan'>cyan:</span>
                                </el-col>
                                <el-col :span="12" >
                                    <el-input  v-model="input_gene_cyan" :placeholder="input_gene_cyan"></el-input>
                                </el-col>
                                <el-col :span="6" >
                                    <el-button type="success" @click.native="ApplyCyan">set</el-button>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="6" >
                                   <span  class='mspan'>magenta:</span>
                                </el-col>
                                <el-col :span="12" >
                                    <el-input  v-model="input_gene_magenta" :placeholder="input_gene_magenta"></el-input>
                                </el-col>
                                <el-col :span="6" >
                                    <el-button type="success" @click.native="ApplyMagenta">set</el-button>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="6" >
                                   <span  class='mspan'>yellow:</span>
                                </el-col>
                                <el-col :span="12" >
                                    <el-input  v-model="input_gene_yellow" :placeholder="input_gene_yellow"></el-input>
                                </el-col>
                                <el-col :span="6" >
                                    <el-button type="success" @click.native="ApplyYellow">set</el-button>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="8" >
                                    <span class='mspan'>Min CutOff:</span>
                                </el-col>
                                <el-col :span="15" >
                                    <el-slider  v-model="min_cutoff" :step="0.5" :min="0" :max="6" @change="changeMinCutoff" show-stops>
                                    </el-slider>
                                </el-col>
                            </el-row>
                         </div>
                         <!-- ----------digital in situ mode end--------------------------------------------------------------- -->
                      <!-- ----------mode setting end --------------------------------------------------------------- -->
                      </el-collapse-item>
                      <el-collapse-item title="Mesh setting:" name="2">
                          <div align="center"   style="margin:3px;  border: 3px solid #ccc;">
                              <el-row style="margin:3px;">
                                  <el-col :span="8" >
                                     <span  class='mspan'>MeshAlpha</span>
                                  </el-col>
                                  <el-col :span="15" >
                                      <el-slider  v-model="mesh_alpha"
                                        :step="0.1" :min="0.0" :max="1" @change="refresh" show-stops>
                                      </el-slider>
                                  </el-col>
                              </el-row>
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <!-- 1. cell table content -->
                                  <el-table class="table" ref="meshTable" style="width:100%;" :show-header='false'
                                    :height='height' :row-key="getMeshKey" :highlight-current-row='true'
                                    :data="tableDataMeshes.slice((currentMeshPage-1)*pageSize,currentMeshPage*pageSize)"
                                    @selection-change="handleMeshSelectionChange"
                                    @row-click="getRowMeshtype" >
                                      <el-table-column :reserve-selection="true" type="selection" ></el-table-column>
                                      <el-table-column property="Meshtype" label="Mesh Type" > </el-table-column>
                                      <el-table-column label="Display" >
                                        <el-button @click="showMeshColorPalette">color</el-button>
                                      </el-table-column>
                                  </el-table>
                                  <el-pagination layout="total, prev, jumper, next" 
                                   :total="this.tableDataMeshes.length" 
                                   :page-size="pageSize" 
                                   :current-page.sync="currentMeshPage">
                                  </el-pagination>
                              </el-row>
                             <!-- start of buttons -->
                             <hr class='dhr'>
                             <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="8" >
                                    <el-button type='success' @click="applyMeshStatus">Apply</el-button>
                                </el-col>
                                <el-col :span="8" >
                                    <el-button type='warning' @click='clearMeshSelect'>Clear</el-button>
                                </el-col>
                                <el-col :span="8" >
                                    <el-button type='primary' @click='resetMeshSelect'>Reset</el-button>
                                </el-col>
                             </el-row>
                             <!-- end of buttons -->
                          </div>
                      </el-collapse-item>
                      <el-collapse-item title="ROI details:" name="3">
                         <!-- ----------roi setting begin --------------------------------------------------------------- -->
                          <div align="center"   style="margin:3px;  border: 3px solid #ccc;">
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="6" >
                                     <span  class='mspan'>Z scale:</span>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-input v-model="tmp_z_scale" placeholder="1"></el-input>
                                  </el-col>
                                  <el-col :span="10" >
                                      <el-button type="success" @click.native="changeZScale">Apply</el-button>
                                  </el-col>
                              </el-row>
                              <hr class="dhr">
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="6" >
                                     <span  class='mspan'>X min:</span>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-input v-model="tmp_x_min" placeholder="0"></el-input>
                                  </el-col>
                                  <el-col :span="10" >
                                      <el-button type="success" @click.native="changeXMin">Apply</el-button>
                                  </el-col>
                              </el-row>
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="6" >
                                     <span  class='mspan'>X max:</span>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-input v-model="tmp_x_max" placeholder="0"></el-input>
                                  </el-col>
                                  <el-col :span="10" >
                                      <el-button type="success" @click.native="changeXMax">Apply</el-button>
                                  </el-col>
                              </el-row>
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="6" >
                                     <span  class='mspan'>Y min:</span>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-input v-model="tmp_y_min" placeholder="0"></el-input>
                                  </el-col>
                                  <el-col :span="10" >
                                      <el-button type="success" @click.native="changeYMin">Apply</el-button>
                                  </el-col>
                              </el-row>
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="6" >
                                     <span  class='mspan'>Y max:</span>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-input v-model="tmp_y_max" placeholder="0"></el-input>
                                  </el-col>
                                  <el-col :span="10" >
                                      <el-button type="success" @click.native="changeYMax">Apply</el-button>
                                  </el-col>
                              </el-row>
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="6" >
                                     <span  class='mspan'>Z min:</span>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-input v-model="tmp_z_min" placeholder="0"></el-input>
                                  </el-col>
                                  <el-col :span="10" >
                                      <el-button type="success" @click.native="changeZMin">Apply</el-button>
                                  </el-col>
                              </el-row>
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="6" >
                                     <span  class='mspan'>Z max:</span>
                                  </el-col>
                                  <el-col :span="8" >
                                      <el-input v-model="tmp_z_max" placeholder="0"></el-input>
                                  </el-col>
                                  <el-col :span="10" >
                                      <el-button type="success" @click.native="changeZMax">Apply</el-button>
                                  </el-col>
                              </el-row>
                              <el-row style="margin-top:3px;margin-bottom:2px">
                                  <el-col :span="24" >
                                      <el-button type="primary" @click.native="resetROI">ResetROI</el-button>
                                  </el-col>
                              </el-row>
                          </div>
                         <!-- ----------roi setting end--------------------------------------------------------------- -->
                      </el-collapse-item>
                      <el-collapse-item title="Display details:" name="4">
                          <!-- ----------display setting begin --------------------------------------------------------------- -->
                          <div align="center" style="margin:3px;  border: 3px solid #ccc;">
                              <!-- animation start -->
                              <el-row style="margin:3px;">
                                  <el-switch  active-text="Animation On" inactive-text="Animation Off"
                                    v-model="animation" @change="refresh" >
                                  </el-switch>
                              </el-row>
                              <!-- animation end -->
                              <!-- projection start -->
                              <el-row style="margin:3px;">
                                  <el-switch  active-text="Perspective" inactive-text="Orthographic"
                                    v-model="projection" @change="refresh" >
                                  </el-switch>
                              </el-row>
                              <!-- animation end -->
                              <!-- light intensive start -->
                              <el-row style="margin:3px;">
                                  <el-col :span="8" >
                                     <span  class='mspan'>LightIntensity</span>
                                  </el-col>
                                  <el-col :span="15" >
                                      <el-slider  v-model="light_intensity"
                                         :step="0.5" :min="0" :max="5" @change="refresh" show-stops>
                                      </el-slider>
                                  </el-col>
                              </el-row>
                              <!-- light internsive end -->
                              <!-- switch background start -->
                              <el-row style="margin:3px;">
                                  <el-switch  active-text="Black theme" inactive-text="White theme"
                                    v-model="black_background" @change="refresh" >
                                  </el-switch>
                              </el-row>
                              <!-- switch background end -->
                              <!-- Draw legend start -->
                              <el-row style="margin:3px;">
                                  <el-switch  active-text="Draw legends" inactive-text="Ignore legends"
                                    v-model="draw_legends" @change="refresh" >
                                  </el-switch>
                              </el-row>
                              <!-- Draw grid start -->
                              <el-row style="margin:3px;">
                                 <el-switch  active-text="Draw grids" inactive-text="Ignore grids"
                                   v-model="draw_grids" @change="refresh" >
                                 </el-switch>
                              </el-row>
                              <!-- Draw grid end -->
                              <!-- Draw box start -->
                              <el-row style="margin:3px;">
                                  <el-switch  align='center' active-text="Draw box" inactive-text="Ignore box"
                                    v-model="draw_box" @change="refresh" >
                                  </el-switch>
                              </el-row>
                              <!-- Draw box end -->
                              <!-- switch symbol size start -->
                              <el-row style="margin:3px;" >
                                  <el-col :span="8" >
                                     <span  class='mspan'>SymbolSize</span>
                                  </el-col>
                                  <el-col :span="15" >
                                      <el-slider  v-model="symbolSize"
                                         :step="1" :min="1" :max="10" @change="refresh" show-stops>
                                      </el-slider>
                                  </el-col>
                              </el-row>
                              <el-row style="margin:3px;">
                                  <el-col :span="8" >
                                     <span  class='mspan'>SymbolAlpha</span>
                                  </el-col>
                                  <el-col :span="15" >
                                      <el-slider  v-model="symbolAlpha"
                                        :step="0.1" :min="0.0" :max="1" @change="refresh" show-stops>
                                      </el-slider>
                                  </el-col>
                              </el-row>
                              <!-- switch symbol size end -->
                          </div>
                          <!-- ----------display setting end--------------------------------------------------------------- -->
                      </el-collapse-item>
                  </el-collapse>
                </div>
              </transition>
              <!-- ----------The setting panel end --------------------------------------------------------------- -->
          </div>
      </el-col>
      <!-- ----------The setting grid end --------------------------------------------------------------- -->
      <!-- ----------The 3D display grid begin --------------------------------------------------------------- -->
      <el-col :span="display_w"  style="height:100%">
        <!-- main window -->
        <div ref="main"  style="border: 3px solid #eee;">
          <!-- I. chart content -->
          <v-chart v-show="model_only" class="chart" resizeable=true :width="chartWidth"  ref="myecharts_model" :option="option_mo" />
          <v-chart v-show="data_valid" class="chart" resizeable=true :width="chartWidth"  ref="myecharts" :option="option"  />
          <div  align='center' style="margin:3px; border: 3px solid #ffffff; " >
              <p tyle="color:red;font-size:40px;">Tip01: Use scroll to zoom. Tip02: Click scroll and hold to drag. Tip03: Left-click and hold to rotate.</p>
              <p tyle="color:white;font-size:40px;">Info01: to achive better visualization effect, unit 1 represent {{unit1}} in data.</p>
          </div>
        </div>
        <!-- end of the main window -->
      </el-col>
      <!-- ----------The 3D display grid end --------------------------------------------------------------- -->
    </el-row>
    <!---------------------------- Main windown end--------------------------------------------------- -->
</div>
</template>

<script>
//import packages begin------------------------------------
import $ from 'jquery';
import * as echarts from 'echarts';
import 'echarts-gl';
import VChart, { THEME_KEY } from "vue-echarts";
import { Draggable } from 'draggable-vue-directive';
import vdr from 'vue-draggable-resizable-gorkys'
import { Sketch } from 'vue-color'
//import packages end------------------------------------

//import colors begin------------------------------------
var COLOR_ALL = require('../confs/discret_color.js');
var COLOR_default = COLOR_ALL.default_colors;
var COLOR_mesh = COLOR_ALL.mesh_colors;
//import colors end------------------------------------

export default {
components: {
  VChart,
  vdr,
  'sketch-picker': Sketch
},
directives: {
  Draggable,
},
props: ['G_Atlas'],
data() {
    return {
      // setting basics begin --------------------------------------
      display_setting: true,
      setting_title :'Hide settings',
      setting_w: 4,
      display_w: 20,
      // collapse details   
      collapseName: '1',
      // setting basics end --------------------------------------

      // working mode begin ----------------------------------------
      all_modes: [
          "CellTypes",
          "GeneExpression",
          "Digital_in_situ",
      ],
      curr_mode : 'CellTypes',
      mode_title: 'CellType setting',
      // mode tags
      is_ct_mode: true,
      is_ge_mode: false,
      is_gc_mode: false,
      box_scale: 0,
      unit1 :1,
      // working mode end ----------------------------------------

      //------------data selection for cell type begin ------
      anno_array : [],
      curr_anno : null,
      curr_legends:{},
      final_clusters: [],
      all_clusters: 0,
      rawdata:null,
      jsondata : null,
      //------------data selection for cell type end ------

      // global page-size for all tables
      pageSize: 5,
      height:'250px',
      //------------cell type table begin ------
      tableDataClusters: [],
      currentPage:1,
      saved_clusters:[], // the selection cache
      currentCellID: '',
      drawer:false,
      //------------cell type table end------

      //------------mesh type table begin ------
      tableDataMeshes:[],
      currentMeshPage:1,
      currentMeshID: '',
      saved_meshes:[],
      drawer_mesh:false,
      final_meshes: [],
      //------------mesh type table end------

      //------------gene expression selection start------
      // 2022-10-10, gene table data
      allTableData: [],
      tableData: [],
      currentGenePage:1,
      // end
      input_gene_id : '',
      curr_gene : "",
      gene_json_raw : null,
      gene_json_data : null,
      curr_max_exp:2,
      fish_url:null,
      //------------gene expression selection end------

      //------------channel selection start------
      input_gene_red:     '',
      input_gene_green:   '',
      input_gene_blue:    '',
      input_gene_gray:    '',
      input_gene_cyan:    '',
      input_gene_magenta: '',
      input_gene_yellow:  '',
      channel_json_raw : {
          red : null,
          green : null,
          blue : null,
          gray : null,
          cyan : null,
          magenta : null,
          yellow: null,
          cyan : null,
      },
      channel_json_data: {
          red : null,
          green : null,
          blue : null,
          gray : null,
          cyan : null,
          magenta : null,
          yellow: null,
          cyan : null,
      },
      channel_keys : ["red",    "green",   "blue",   "gray",   "cyan",   "magenta", "yellow"],
      channel_colors : {
          red    :"#ff0000",
          green  :"#00ff00",
          blue   :"#0000ff",
          gray   :"#888888",
          cyan   :"#00ffff",
          magenta:"#ff00ff",
          yellow :"#ffff00"
      },
      min_cutoff : 3,
      //------------channel selection end------

      //------------model data : mesh begin ------
      mesh_json : null,
      mesh_alpha: 0.5,
      mesh_conf : {
        names:    [],
        legends : [],
        colors :  [],
      },
      //------------model data : mesh begin ------

      //------------color legends confs begin------
      // test color 
      COLOR_default :COLOR_default,
      COLOR_ALL2: COLOR_default,
      COLOR_mesh: COLOR_mesh,
      current_color_all: null,
      color: '',
      //------------color legends confs end------

      //------------roi confs begin------
      z_scale:1,
      tmp_z_scale:1,
      // roi
      tmp_x_min:0,
      tmp_y_min:0,
      tmp_z_min:0,
      tmp_x_max:300,
      tmp_y_max:300,
      tmp_z_max:300,
      x_min:0,
      y_min:0,
      z_min:0,
      x_max:300,
      y_max:300,
      z_max:300,
      //------------roi confs end------
      // drawing details conf start ----------
      animation:false,
      projection:true,
      black_background:true,
      draw_legends:true,
      draw_grids:false,
      draw_box :true,
      adjusted_posture:true,
      symbolSize:2,
      symbolAlpha:1.0,
      light_intensity:2.0,
      // drawing details conf end----------
      // echarts option begin -------------
      chartWidth:'100%',
      model_only:true,
      data_valid:false,
      curr_blender: "source-over",
      option_mo: {
        backgroundColor:'#000000',
        title : {
            text : 'Loading data now ...',
            left: "center",
            top: "center",
            textStyle: {
               color: '#cccccc'
            },
        }
      },
      option: {
        backgroundColor:'#000000',
        title : {
            text : 'Loading data now ...',
            left: "center",
            top: "center",
            textStyle: {
               color: '#cccccc'
            },
        }
      },
    }
  },
  methods: {
    // ------------------ resize page begin ----------------------
    resize_option() {
       this.$nextTick(() => {
           this.$refs.myecharts.resize();
           this.$refs.myecharts_model.resize();
       });
    },
    span_setting() {
        if( this.display_setting == true ) {
            this.display_setting = false;
            this.setting_w = 0;
            this.display_w = 24 ;
            this.setting_title = '';
            this.resize_option()
        } else {
            this.display_setting = true;
            this.setting_w = 4;
            this.display_w = 20 ;
            this.setting_title = 'Hide Settings'
            this.resize_option()
        }
    },
    // ------------------ resize page end----------------------
    // ------------------ basic conf functions begin----------------------
    OnChangeMode(){
        this.mode_title = this.curr_mode+" setting";
        this.update_option_deep();
        console.log(this.curr_mode)
        if( this.curr_mode == "CellTypes") {
            this.is_ct_mode = true;
            this.is_gc_mode = false;
            this.is_ge_mode = false;
            // set a cell type
            this.anno_array = this.G_Atlas['summary']['annokeys'];
            this.curr_anno = this.anno_array[0];
            var tempkey = this.curr_anno+'_int2legend';
            this.curr_legends = this.G_Atlas['summary']['annomapper'][tempkey];
            this.OnChangeAnno();
        }else if ( this.curr_mode == "GeneExpression" ) {
            this.is_ct_mode = false;
            this.is_gc_mode = false;
            this.is_ge_mode = true;
            this.loadGeneTable();
        } else {
            this.is_ct_mode = false;
            this.is_gc_mode = true;
            this.is_ge_mode = false;
        }
    },
    // ------------------ basic conf functions end----------------------

    // ---- init atlas basics begin ---------------------------------------
    init_scale() {
        var box = this.G_Atlas['summary']['box']
        var xlen = box['xmax'] - box['xmin'] +1
        var ylen = box['ymax'] - box['ymin'] +1
        var zlen = box['zmax'] - box['zmin'] +1
        var max_length = Math.max(xlen,ylen,zlen);
        this.box_scale = 1;
        if( max_length > 300.0 ) this.box_scale = 1.0/max_length*300;
        if( max_length < 100.0 ) this.box_scale = 1.0/max_length*100;
        console.log(this.box_scale);
        this.unit1 = 1/this.box_scale;
    },
    InitAtlas() {
        // Init the celltype setting panel
        this.init_scale();
        this.resetROIdata();
        this.resetMesh()
        this.OnChangeMode();
    },
    // ---- init atlas basics end ---------------------------------------

    // ---- celltype conf panel begin -----------------------------------
    cleanCellTypeBuffer(){
      this.jsondata = null ;
      this.rawdata = null;
    },

    InitAnnoTable() {
        var summary = this.G_Atlas['summary'];
        var tempkey = this.curr_anno+'_legend2int';
        var mapper = summary['annomapper'][tempkey]
        var legend_num = 0;
        var new_tableDataClusters = []
        for (var key in mapper) {
            new_tableDataClusters.push({ID: mapper[key], Celltype:key });
            legend_num = legend_num + 1;
        }
        this.tableDataClusters = new_tableDataClusters;
        this.all_clusters = legend_num;
    },
    resetCellTypeScattters(){
        var used_url = this.G_Atlas["anno_url"] +"/"+this.curr_anno+".json"
        var self = this;
        console.log(used_url);
        $.getJSON(used_url,function(_data) {
          console.log('json loaded');
          self.setAnnoData(_data);
          self.update_option_deep();
        });
    },
    setAnnoData(_data) {
        var curr_draw_datas= [];
        this.final_clusters = new Array(this.all_clusters).fill(0);
        for(var i=0;i<this.all_clusters;i++){
            curr_draw_datas.push([])
        }
        // --------- iterate through real data (long)
        for(var j=0 ; j< _data.length; j++){
          var curr_item = _data[j];
          //console.log(curr_item);
          curr_draw_datas[parseInt(curr_item[3])].push([curr_item[0]*this.box_scale,curr_item[1]*this.box_scale,curr_item[2]*this.box_scale]);
        } // end of for _data
        // -------- mark empty group
        for (var i = 0; i < this.all_clusters; i++){
          if (curr_draw_datas[i].length == 0) {
            this.final_clusters[i] = 0;
          }else{
            this.final_clusters[i] = 1;
          }
        }
        this.rawdata = curr_draw_datas;
        this.jsondata = curr_draw_datas;
        this.data_valid = true
    },
    OnChangeAnno(){
        this.cleanCellTypeBuffer();
        this.COLOR_ALL2 = this.COLOR_default;
        this.InitAnnoTable();
        this.resetCellTypeScattters();
        this.update_option_deep();
    },
    // ---- celltype conf panel end -----------------------------------


    //----------- gene select functions start -------------//
    loadGeneTable(){
        if ( this.allTableData.length <1 ) {
            // 2022-10-10 liyao: load gene id mapping table
            var self = this;
            $.getJSON(this.G_Atlas["gene_url"], function(_data){
                var td = []
                for( var i = 0; i<_data.length; i++){
                    td.push({'smed':_data[i]})
                }
                self.tableData = td;
                self.allTableData = td;
            });
        }
    },
    updateTable(){
        // 2022-10-10  liyao:search gene
        var new_tableData = [];
        var arrayLength = this.allTableData.length;
        for (var i = 0; i < arrayLength; i++) {
            if (this.allTableData[i]['smed'].includes(this.input_gene_id)){
                new_tableData.push(this.allTableData[i]);
            }
        }
        this.tableData = new_tableData;
    },
    handleRow(row,event,column){
        this.input_gene_id = row.smed;
        this.updateTable();
        this.refreshGene(this.input_gene_id,true);
    },
    refreshGene(gname, force) {
        if ( gname == null || gname == "" ) return; 
        if (this.curr_gene != gname || force ) {
            this.gene_json_raw = null;
            this.gene_json_data = null;
            this.update_option_deep();
            this.curr_gene = gname;
            var used_url = this.G_Atlas['genes_url'] +'/'+this.curr_gene+".json";
            var self = this;
            $.getJSON(used_url,function(_data) {
              self.setGeneData(_data);
              self.update_option_deep();
            });
        }
    },
    setGeneData(_data) {
        console.log('get gene json loaded');
        var gene_xyz= [];
        for(var j=0 ; j< _data.length; j++)
        {
            var curr_item = _data[j];
            if( parseInt(curr_item[3])+1>  this.curr_max_exp)
                this.curr_max_exp = parseInt(curr_item[3])+1;
            gene_xyz.push( [curr_item[0]*this.box_scale,curr_item[1]*this.box_scale,curr_item[2]*this.box_scale,curr_item[3]]);
        }
        this.gene_json_raw= gene_xyz;
        this.updateJsonData();
    },
    //----------- gene select functions end -------------//


    // ------------------ functions for mesh table begin -----------------
    showMeshColorPalette(){
      this.drawer_mesh = true; 
    },
    getRowMeshtype(row){
      this.currentMeshID = row.ID;
    },
    getMeshKey(row) {
        return row.Meshtype;
    },
    closeMeshColor() {
      this.drawer_mesh = false;
    },
    applyMeshColor() {
      console.log(this.currentMeshID)
      this.current_color_all = this.COLOR_mesh;
      this.current_color_all[this.currentMeshID] = this.color;
      this.option=this.getOption() ;
      this.drawer_mesh = false;
    },
    OnDrawerMeshClose(done) {
        this.drawer_mesh = false;
        done();
    },
    handleMeshSelectionChange(val) {
      // change color when check box
      // 2. get row id when use click selection box
      if (val.length > 0){
        this.currentMeshID = val[val.length -1].ID;
      }
      // save cluster highlight status in an array
      var tmp_clusters= new Array(this.tableDataMeshes.length).fill(0);
      for( var i = 0 ; i < val.length ; i++) {
          tmp_clusters[val[i].ID]=1;
      }
      this.saved_meshes =tmp_clusters;
      console.log(this.saved_meshes)
    },
    resetMeshSelect(){
      this.final_meshes=new Array(this.tableDataMeshes.length).fill(1);
      this.update_option();
    },
    clearMeshSelect() {
      this.currentMeshID = '';
      this.$refs.meshTable.clearSelection();
    },
    applyMeshStatus() {
      this.final_meshes =this.saved_meshes;
      this.update_option();
    },
    // ------------------ functions for mesh table end -----------------

    // ------------------ functions for cell type table begin -----------------
    OnDrawerClose(done){
        this.drawer= false;
        done();
    },
    getRowKey(row) {
      return row.Celltype
    },
    handleSizeChange (size) {
      this.pageSize = size;
    },
    handleCurrentChange (currentpage){
      this.currentPage = currentpage;
    },
    resetSelect(){
      this.final_clusters=new Array(this.all_clusters).fill(1);
      this.update_option();
    },
    clearSelect () {
      this.currentCellID = '';
      this.$refs.clusterTable.clearSelection();
    },
    applyStatus() {
      this.final_clusters=this.saved_clusters;
      this.update_option();
    },
    handleSelectionChange(val) {
      // change color when check box
      // 2. get row id when use click selection box
      if (val.length > 0){
        this.currentCellID = val[val.length -1].ID;
      }
      // save cluster highlight status in an array
      var tmp_clusters= new Array(this.all_clusters).fill(0);
      for( var i = 0 ; i < val.length ; i++) {
          tmp_clusters[val[i].ID]=1;
      }
      this.saved_clusters=tmp_clusters;
    },
    //----------- color palette of cell type start -------------//
    getRowCelltype(row){
      this.currentCellID = row.ID;
    },
    showColorPalette(){
      this.drawer = true; 
    },
    colorValueChange (val) {
     this.color = val.hex;
    },
    applyColor(){
      console.log(this.currentCellID)
      this.current_color_all = this.COLOR_ALL2;
      this.current_color_all[this.currentCellID] = this.color;
      this.option=this.getOption();
      this.drawer = false;
    },
    closeColor(){
      this.drawer = false;
    },
    //-------------color palette of celltype ends------------------//
    // ------------------ functions for cell type table end -----------------

    // ------------------ functions for update echarts begin -----------------
    update_option(){
        if( this.data_valid == true)
            this.option = this.getOption();
        else
            this.option_mo = this.getOption();
        this.resize_option();
    },
    update_option_deep(){
        if( this.data_valid == true)
            this.option = this.getOption();
        else
            this.option_mo = this.getOption();
        if( this.data_valid ){
            this.$refs.myecharts.clear();
            this.$refs.myecharts.setOption(this.getOption(),true,false);
        }else {
            this.$refs.myecharts_model.clear();
            this.$refs.myecharts_model.setOption(this.getOption(),true,false);
        }
        this.resize_option();
    },
    // ------------------ functions for update echarts end -----------------
    //-------------3d box conf start-------------------//
    getXMin(){
        var summary = this.G_Atlas['summary'];
        return summary['box']['xmin'] *this.box_scale;
    },
    getXMax(){
        var summary = this.G_Atlas['summary'];
        return summary['box']['xmax'] *this.box_scale;
    },
    getYMin(){
        var summary = this.G_Atlas['summary'];
        return summary['box']['ymin'] *this.box_scale;
    },
    getYMax(){
        var summary = this.G_Atlas['summary'];
        return summary['box']['ymax'] *this.box_scale;
    },
    getZMin(){
        var summary = this.G_Atlas['summary'];
        return summary['box']['zmin'] *this.box_scale;
    },
    getZMax(){
        var summary = this.G_Atlas['summary'];
        return summary['box']['zmax'] *this.box_scale;
    },
    getWidth(){
      return this.getXMax()-this.getXMin()+2;
    },
    getDepth() {
      return this.getZMax()-this.getZMin()+2;
    },
    getHeight() {
      return this.getYMax()-this.getYMin()+2;
    },
    //-------------3d box conf end -------------------//

    //------------function of channel selection start------
    ApplyRed() {
        this.loadOneChannelGene(this.input_gene_red,"red")
    },
    ApplyGreen() {
        this.loadOneChannelGene(this.input_gene_green,"green")
    },
    ApplyBlue() {
        this.loadOneChannelGene(this.input_gene_blue,"blue")
    },
    ApplyGray() {
        this.loadOneChannelGene(this.input_gene_gray,"gray")
    },
    ApplyCyan() {
        this.loadOneChannelGene(this.input_gene_cyan,"cyan")
    },
    ApplyMagenta() {
        this.loadOneChannelGene(this.input_gene_magenta,"magenta")
    },
    ApplyYellow() {
        this.loadOneChannelGene(this.input_gene_yellow,"yellow")
    },
    loadOneChannelGene(input_gene, key) {
        if ( this.channel_json_raw[key] != null ) {
            this.channel_json_raw[key] = null;
            this.channel_json_data[key] = null;
            this.update_option_deep()
        }
        if ( input_gene != "" ) {
            var used_url = this.G_Atlas['genes_url'] +'/'+input_gene+".json";
            var self = this;
            var kkey = key;
            $.getJSON(used_url,function(_data) {
              self.setChannelGeneData(_data,kkey);
              self.update_option_deep();
            });
        }
    },
    changeMinCutoff() {
        this.updateJsonData();
        this.update_option();
    },
    resetDefaultChannelGene() {

    },
    getChannelName(key){
        if(key == "red") return this.input_gene_red;
        if(key == "green") return this.input_gene_green;
        if(key == "blue") return this.input_gene_blue;
        if(key == "gray") return this.input_gene_gray;
        if(key == "cyan") return this.input_gene_cyan;
        if(key == "magenta") return this.input_gene_magenta;
        if(key == "yellow") return this.input_gene_yellow;
        return "";
    },
    resetChannel() {
        for( var i=0;i<this.channel_keys.length; i++ ) {
            var key = this.channel_keys[i];
            this.channel_json_raw[key] = null;
            this.channel_json_data[key] = null;
        }
        this.ApplyRed();
        this.ApplyGreen();
        this.ApplyBlue();
        this.ApplyGray();
        this.ApplyCyan();
        this.ApplyMagenta();
        this.ApplyYellow();
        this.update_option_deep();
    },
    setChannelGeneData(_data,key) {
        var gene_xyz = [];
        for(var j=0 ; j< _data.length; j++) {
            var curr_item = _data[j];
            gene_xyz.push([curr_item[0]*this.box_scale,curr_item[1]*this.box_scale,curr_item[2]*this.box_scale,curr_item[3]]);
        }
        this.channel_json_raw[key] = gene_xyz;
        this.update_onechannel_gene(key);
    },
    update_onechannel_gene(key) {
        this.channel_json_data[key] = null;
        if(this.channel_json_raw[key] != null) {
            var curr_draw_datas = [];
            var infoarry = this.channel_json_raw[key]
            for(var i =0;i<infoarry.length; i++) {
               var info = infoarry[i];
               if( info[0]<this.x_min) continue;
               if( info[1]<this.y_min) continue;
               if( info[2]<this.z_min) continue;
               if( info[0]>this.x_max) continue;
               if( info[1]>this.y_max) continue;
               if( info[2]>this.z_max) continue;
               if( info[3]<this.min_cutoff)continue;
               curr_draw_datas.push(info)
            }
            this.channel_json_data[key] = curr_draw_datas;
        }
    },
    //------------function of channel selection end------

    //-------------mesh managerment start -------------------//
    cleanMesh() {
      this.mesh_json = null;
      this.mesh_conf.names = [];
      this.mesh_conf.legends = [];
      this.mesh_conf.colors = this.COLOR_mesh;
    },
    resetMesh() {
      this.cleanMesh();
      var used_url = this.G_Atlas['meshes_url'];
      console.log(used_url);
      var self = this;
      $.getJSON(used_url,function(_data) {
        console.log("mesh loaded");
        console.log(_data);
        self.setMeshData(_data);
        self.$nextTick(() => {
            self.update_option_deep();
        });
      });
    },
    setMeshData(_data) {
        if( _data[0].length < 1 )
            return ;
        var new_tableDataMeshes = []
        this.mesh_json = {}
        for(var i = 0 ; i< _data[0].length; i++){
            var mesh = _data[0][i];
            new_tableDataMeshes.push({ID: i, Meshtype:mesh});
            this.mesh_conf.names.push(mesh);
            this.mesh_conf.legends.push(mesh+"_mesh");
            this.mesh_json[mesh] = {}
            var xyz =  _data[1][i];
            var vectors = []
            for( var j = 0 ; j < xyz.length; j++){
                vectors.push([xyz[j][0]*this.box_scale,xyz[j][1]*this.box_scale, xyz[j][2]*this.box_scale] );
            }
            this.mesh_json[mesh]['xyz'] = vectors;
            this.mesh_json[mesh]['ijk'] = _data[2][i];
        }
        this.tableDataMeshes = new_tableDataMeshes;
        this.final_meshes=new Array(this.tableDataMeshes.length).fill(1);
    },
    //-------------mesh managerment end -------------------//

    //-------------configure ROI start -------------------//
    changeZScale(){
      this.z_scale = this.tmp_z_scale;
      this.update_option();
    },
    resetROIdata(){
      // reset-roi
      this.z_scale = 1;this.tmp_z_scale=1;
      this.x_min = this.getXMin();
      this.y_min = this.getYMin();
      this.z_min = this.getZMin();
      this.tmp_x_min=this.x_min;
      this.tmp_y_min=this.y_min;
      this.tmp_z_min=this.z_min;
      this.x_max = this.getXMax();
      this.y_max = this.getYMax();
      this.z_max = this.getZMax();
      this.tmp_x_max = this.x_max; 
      this.tmp_y_max = this.y_max;
      this.tmp_z_max = this.z_max;
    },
    reset_channel_jsons(){

    },
    resetROI(){
      this.resetROIdata();
      if(this.curr_mode == "CellTypes")
          this.jsondata = this.rawdata;
      else if (this.curr_mode == "GeneExpression")
          this.gene_json_data = this.gene_json_raw;
      else
          this.reset_channel_jsons();
      this.update_option();
    },
    updateJsonData(){
      if(this.curr_mode == "CellTypes")
          this.updateAnnoJsonData();
      else if (this.curr_mode == "GeneExpression")
          this.updateGeneJsonData();
      else 
          this.updateChannelJsons();
    },
    updateChannelJsons(){
        for(var i = 0; i < this.channel_keys.length; i++){
            this.update_onechannel_gene(this.channel_keys[i])
        }
    },
    updateGeneJsonData(){
      var curr_draw_datas = [];
      for(var i =0;i<this.gene_json_raw.length; i++){
         var info = this.gene_json_raw[i]
         if( info[0]<this.x_min) continue;
         if( info[1]<this.y_min) continue;
         if( info[2]<this.z_min) continue;
         if( info[0]>this.x_max) continue;
         if( info[1]>this.y_max) continue;
         if( info[2]>this.z_max) continue;
         //if( info[3]<this.smallestExpression)continue;
         //if( info[3]>this.largestExpression)continue;
         curr_draw_datas.push(info)
      }
      this.gene_json_data = curr_draw_datas;
    },
    updateAnnoJsonData(){
      var curr_draw_datas = [];
      for(var i =0;i<this.all_clusters;i++)
          curr_draw_datas.push([['x','y','z']]);
      for(var i =0;i<this.all_clusters;i++){
        for(var j =1;j<this.rawdata[i].length; j++){
           var info = this.rawdata[i][j];
           if( info[0]<this.x_min) continue;
           if( info[1]<this.y_min) continue;
           if( info[2]<this.z_min) continue;
           if( info[0]>this.x_max) continue;
           if( info[1]>this.y_max) continue;
           if( info[2]>this.z_max) continue;
           curr_draw_datas[i].push(info)
        }
      }
      this.jsondata = curr_draw_datas;
    },
    changeXMin(){
      var txmin = Number(this.tmp_x_min);
      if(this.x_min != txmin){
        if(txmin<this.getXMin())                      this.x_min = this.getXMin();
        else if (txmin>this.x_max-1)                  this.x_min = this.x_max-1;
        else if (txmin>this.getXMax()-1)              this.x_min = this.getXMax()-1;
        else                                          this.x_min = txmin;
        this.updateJsonData();
        this.update_option();
      }
    },
    changeXMax(){
      var txmax = Number(this.tmp_x_max);
      if(this.x_max != txmax){
        if(txmax<this.getXMin()+1)                    this.x_max = this.getXMin()+1;
        else if (txmax<Number(this.x_min)+1)          this.x_max = Number(this.x_min) +1;
        else if (txmax>this.getXMax())                this.x_max = this.getXMax();
        else                                          this.x_max = txmax;
        console.log(this.x_max);
        this.updateJsonData();
        this.update_option();
      }
    },
    changeYMin(){
      var tymin = Number(this.tmp_y_min);
      if(this.y_min != tymin){
        if(tymin<this.getYMin())                      this.y_min = this.getYMin();
        else if (tymin>this.y_max-1)                  this.y_min = this.y_max-1;
        else if (tymin>this.getYMax()-1)              this.y_min = this.getYMax()-1;
        else                                          this.y_min = tymin;
        console.log(this.y_min);
        this.updateJsonData();
        this.update_option();
      }
    },
    changeYMax(){
      var tymax = Number(this.tmp_y_max);
      if(this.y_max != tymax) {
        if(tymax<this.getYMin()+1)                    this.y_max = this.getYMin()+1;
        else if (tymax<Number(this.y_min)+1)          this.y_max = Number(this.y_min) +1;
        else if (tymax>this.getYMax())                this.y_max = this.getYMax();
        else                                          this.y_max = tymax;
        console.log(this.y_max);
        this.updateJsonData();
        this.update_option();
      }
    },
    changeZMin(){
      var tzmin = Number(this.tmp_z_min);
      if(this.z_min != tzmin){
        if(tzmin<this.getZMin())                      this.z_min = this.getZMin();
        else if (tzmin>this.z_max-1)                  this.z_min = this.z_max-1;
        else if (tzmin>this.getZMax()-1)              this.z_min = this.getZMax()-1;
        else                                          this.z_min = tzmin;
        this.updateJsonData();
        this.update_option()
      }
    },
    changeZMax(){
      var tzmax = Number(this.tmp_z_max);
      if(this.z_max != tzmax){
        if(tzmax<this.getZMin()+1)                    this.z_max = this.getZMin()+1;
        else if (tzmax<Number(this.z_min)+1)          this.z_max = Number(this.z_min) +1;
        else if (tzmax>this.getZMax())                this.z_max = this.getZMax();
        else                                          this.z_max = tzmax;
        this.updateJsonData();
        this.update_option();
      }
    },
    //-------------configure ROI end -------------------//

    //-------------configure display_setting begin -------------------//
    refresh(){
      this.update_option();
    },
    //-------------configure display_setting end-------------------//
    getLoadingOption(bk_color,ft_color){
        var curr_title = 'Loading data now ...';
        return {
          backgroundColor:bk_color,
           title :{
            text : curr_title,
            left: "center",
            top: "center",
            textStyle: {
               color: ft_color
            },
          }
        };
    },
    getMeshSerie(){
        if( this.mesh_json != null ){
          console.log('draw mesh');
          var legend_list = [];
          var series_list = [];
          var legend_show = {};
          for(var i = 0; i<this.mesh_conf.names.length; i++){
              var curr_name = this.mesh_conf.names[i];
              var curr_legend_name = this.mesh_conf.legends[i];
              var curr_color = this.mesh_conf.colors[i];
              if ( this.mesh_json[curr_name]['xyz'].length == 0 ) 
                  continue;
              //console.log('curr_legend_name');
              legend_list.push(curr_legend_name);
              if(this.final_meshes[i] == 1 )
                  legend_show[curr_legend_name]=true;
              else
                  legend_show[curr_legend_name]=false;
              var one_series = {
                  name : curr_legend_name,
                  type : 'surface',
                  data: this.mesh_json[curr_name]['xyz'],
                  isMesh :true,
                  shading:'lambert',
                  dataShape:[2,3],
                  indices : this.mesh_json[curr_name]['ijk'],
                  color: curr_color,
                  borderWidth :1,
                  opacity:this.mesh_alpha,
                  silent:true,
              };
              series_list.push(one_series);
          }
          var ret = {
              series_list: series_list,
              legend_list : legend_list,
              legend_show:legend_show,
          };
          //console.log(ret)
          return ret;
        } else {
          return null;
        }
    },
    getGeneExpSerie(){
        if(this.gene_json_data == null)
            return null;
        var legend_name = this.curr_gene;
        var one_series = {
            name : this.curr_gene,
            type : 'scatter3D',
            dimensions: [ 'x','y','z' ,'exp'],
            data: this.gene_json_data,
            symbolSize: this.symbolSize,
            symbolAlpha: this.symbolAlpha,
            itemStyle: {
              borderWidth: 0,
            },
        };
        var visualMap= [
            {
               type: 'continuous',
               min: 0,
               max: this.curr_max_exp,
               dimension: 3, // the fourth dimension of series.data (i.e. value[3]) is mapped
               seriesIndex: 0, // The first series is mapped.
               orient: 'vertical',
               right: 5,
               top: 'center',
               calculable: true,
               inRange: {
                  color: ['blue', 'white', 'red'], // A list of colors that defines the graph color mapping
               },
               textStyle: {
                  color: '#cccccc'
               },
        }];
        var ret = {
            legend_name : legend_name,
            one_series : one_series,
            visualMap:visualMap,
        };
        return ret ;
    },
    getChannelSeries(){
        var series_list = [];
        var legend_list = [];
        var curr_color ;
        for( var i = 0 ; i<this.channel_keys.length; i++ )
        {
            var key = this.channel_keys[i];
            var curr_draw_datas = this.channel_json_data[key];
            if ( curr_draw_datas == null )
                continue;
            var curr_legend_name = this.getChannelName(key);
            if( curr_legend_name == "" ) 
                continue;
            legend_list.push(curr_legend_name);
            curr_color = this.channel_colors[key];
            var one_series = {
                name : curr_legend_name,
                type : 'scatter3D',
                data:  curr_draw_datas,
                symbol:'circle',
                symbolSize: this.symbolSize,
                itemStyle: {
                  borderWidth: 0,
                  color: curr_color,
                  opacity: this.symbolAlpha,
                },
                blendMode:this.curr_blender,
                silent : true,
            };
            series_list.push(one_series);
        } // end of for final_clusters.length
        var ret = {
            series_list : series_list,
            legend_list : legend_list,
        };
        if(series_list.length <1 ) return null;
        return ret;
    },
    getScatterSeries(){
        if(this.jsondata == null)
            return null;
        var curr_draw_datas = this.jsondata;
        console.log('knowing json loaded');
        var series_list = [];
        var legend_list = [];
        var legend_show = {};
        var curr_color ;
        console.log('start series');
        for( var i = 0 ; i<this.all_clusters; i++ )
        {
          var curr_legend_name = this.curr_legends[i];
          var the_data = curr_draw_datas[i];
          if(this.final_clusters[i] == 0 || the_data.length==0){
            legend_show[curr_legend_name] = false;
          } else {
            legend_show[curr_legend_name] = true;
          }
          legend_list.push(curr_legend_name);
          curr_color = this.COLOR_ALL2[i];
          var one_series = {
              name : legend_list[i],
              type : 'scatter3D',
              data: the_data,
              symbol:'circle',
              //zlevel: -20,
              symbolSize: this.symbolSize,
              itemStyle: {
                //borderColor: curr_color,
                borderWidth: 0,
                color: curr_color,
                opacity: this.symbolAlpha,
              },
              blendMode:this.curr_blender,
              silent : true,
          };
          series_list.push(one_series);
        } // end of for final_clusters.length
        var ret = {
            series_list : series_list,
            legend_list : legend_list,
            legend_show : legend_show,
        };
        return ret;
    },
    getBoxSeries(used_xmin,used_ymin,used_zmin,used_xmax,used_ymax,used_zmax){
        if ( this.draw_box == true) {
            var box_series = {
                type: 'line3D',
                lineStyle: {
                    width: 3,
                    color: '#aaaaaa',
                },
                data: [
                    [used_xmin, used_ymin, used_zmin],
                    [used_xmax, used_ymin, used_zmin],
                    [used_xmax, used_ymax, used_zmin],
                    [used_xmin, used_ymax, used_zmin],
                    [used_xmin, used_ymin, used_zmin], // rect01
                    [used_xmin, used_ymin, used_zmax],
                    [used_xmax, used_ymin, used_zmax],
                    [used_xmax, used_ymin, used_zmin],
                    [used_xmin, used_ymin, used_zmin], // rect02
                    [used_xmax, used_ymin, used_zmin],
                    [used_xmax, used_ymax, used_zmin],
                    [used_xmax, used_ymax, used_zmax], 
                    [used_xmax, used_ymin, used_zmax], // rect 03
                    [used_xmin, used_ymin, used_zmax],
                    [used_xmin, used_ymax, used_zmax],
                    [used_xmax, used_ymax, used_zmax], // rect 04
                    [used_xmax, used_ymax, used_zmin],
                    [used_xmin, used_ymax, used_zmin],
                    [used_xmin, used_ymax, used_zmax],
                ]
            };
            return box_series;
        } else {
            return null;
        }
    },
    getOption(){
      // set colors 
      var bk_color = '#000000';
      var ft_color = '#cccccc';
      if ( this.black_background == false ) {
        bk_color = '#FFFFFF';
        ft_color = '#333333';
      }
      var mesh_serie = null;
      var scatter_series = null ;
      var gene_exp_serie = null;
      var channel_series = null;
      mesh_serie = this.getMeshSerie();
      if( this.curr_mode == "CellTypes" ) 
          scatter_series = this.getScatterSeries();
      else if ( this.curr_mode == "GeneExpression" )
          gene_exp_serie = this.getGeneExpSerie();
      else 
          channel_series = this.getChannelSeries();
      // Draw loading if necessary
      if ( mesh_serie == null && scatter_series == null && gene_exp_serie == null && channel_series == null ) {
          this.data_valid = false;
          this.model_only = true;
          return this.getLoadingOption(bk_color,ft_color);
      }
      else {
        var series_list = [];
        var legend_list = [];
        var legend_show = {};
        //Draw scatters
        var tips = '';
        if( this.curr_mode == "CellTypes" ) {
            if( scatter_series != null ){
                 for(var i = 0; i < scatter_series.series_list.length; i++){
                     series_list.push(scatter_series.series_list[i])
                 }
                 for(var i = 0; i < scatter_series.legend_list.length; i++){
                     legend_list.push(scatter_series.legend_list[i]);
                 }
                 legend_show = scatter_series.legend_show;
                 this.data_valid = true;
                 this.model_only = false;
            } else {
                 this.data_valid = false;
                 this.model_only = true;
                 tips = 'Model done, still loading scatters ...';
            }
         } else if ( this.curr_mode == "GeneExpression" ){
            if( gene_exp_serie != null){
                 this.data_valid = true;
                 this.model_only = false;
                 series_list.push(gene_exp_serie.one_series);
                 legend_list.push(gene_exp_serie.legend_name);
                 legend_show[gene_exp_serie.legend_name] = true;
            } else {
                 this.data_valid = false;
                 this.model_only = true;
                 tips = 'Model done, still loading gene data ...';
            }
         } else {
            if( channel_series!=null){
                for(var i = 0; i < channel_series.series_list.length; i++){
                    series_list.push(channel_series.series_list[i])
                }
                for(var i = 0; i < channel_series.legend_list.length; i++){
                    legend_list.push(channel_series.legend_list[i]);
                }
                this.data_valid = true;
                this.model_only = false;
            } else {
                 this.data_valid = false;
                 this.model_only = true;
            }
         }
          // Draw mesh
        if(mesh_serie != null){
            for(var i = 0;i< mesh_serie.series_list.length; i++){
                series_list.push(mesh_serie.series_list[i]);
            }
            for(var i = 0;i< mesh_serie.legend_list.length; i++){
                legend_list.push(mesh_serie.legend_list[i]);
                legend_show[mesh_serie.legend_list[i]] = mesh_serie.legend_show[mesh_serie.legend_list[i]];
            }
        }
        // set 3D space 
        var used_xmin = this.getXMin();
        var used_xmax = this.getXMax();
        var used_ymin = this.getYMin();
        var used_ymax = this.getYMax();
        var used_zmin = this.getZMin();
        var used_zmax = this.getZMax();
        var boxWidth  = this.getWidth();
        var boxHeight = this.getDepth()*this.z_scale;
        var boxDepth  = this.getHeight();

        var box_series = this.getBoxSeries(used_xmin,
                                      used_ymin,
                                      used_zmin,
                                      used_xmax,
                                      used_ymax,
                                      used_zmax);
        if(box_series!= null){
            series_list.push(box_series)
        }
        var curr_projection = '';
        if(this.projection )
            curr_projection = 'perspective';
        else
            curr_projection = 'orthographic';
        var curr_distance = 200;
        if(this.box_scale < 1 ) 
            curr_distance = 400;
        else 
            curr_distance = 150;
        var opt={
          backgroundColor:bk_color,
          title :{
            text : tips,
            left: "center",
            top : "bottom",
            textStyle: {
               color: ft_color
            },
          },
          legend :{
            show: this.draw_legends,
            data:legend_list,
            selected: legend_show,
            textStyle: {
              color:ft_color,
              fontSize:24,
            }
          },
          toolbox: {
             show: true,
             feature: {
                 restore : {},
                 saveAsImage: {},
             },
             iconStyle: {
                 borderColor: '#fff',
                 borderWidth : 2,
             },
          },
          tooltip: {},
          xAxis3D: {
            name: 'x',
            scale:1,
            type: 'value',
            min: used_xmin,
            max: used_xmax,
          },
          yAxis3D: {
            name: 'y',
            scale:1,
            type: 'value',
            min: used_ymin,
            max: used_ymax,
          },
          zAxis3D: {
            name: 'z',
            scale:1,
            type: 'value',
            min: used_zmin,
            max: used_zmax,
          },
          grid3D: {
            show: this.draw_grids,
            boxWidth: boxWidth,
            boxHeight: boxHeight ,
            boxDepth: boxDepth , 
            light: {
              main: {
                 shadow: false,
                 intensity: this.light_intensity,
                 quality: 'high'
              },
              ambient:{
                 intensity: this.light_intensity,
              },
              ambientCubemap:{
                 intensity: this.light_intensity,
              },
            },
            axisLine: {
              lineStyle: {
                color:ft_color,
              }
            },
            axisPointer: {
              lineStyle: {
                color: ft_color
              }
            },
          viewControl: {
              projection: curr_projection,
              autoRotate: this.animation,
              minDistance: 1,
              distance: curr_distance,
              maxDistance: 2000,
              minAlpha:-3600,
              maxAlpha:3600,
              minBeta:-3600,
              maxBeta:3600,
            }
          },
          series: series_list
        }; // end of var opt
        if ( this.curr_mode == "GeneExpression" && gene_exp_serie != null ){
            opt.visualMap = gene_exp_serie.visualMap;
        }
        console.log('reset option');
        return opt;
      } // end of else.
    } // end of function option.
  },
  mounted() {
    window.addEventListener("resize", this.resize_option,true);
    this.InitAtlas();
  },
};
</script>

<style>
.floatsetting{
    position: fixed;
    z-index: 999;
    left: 0;
    top: 70;
}
.dhr{
    border:1px dashed #888;
}
.mspan{
  display: inline-block;
  margin-top: 10px;
  vertical-align: middle;
  text-align:center;
}
.inline_item {
  display: inline-block;
  margin-left: 20px;
  vertical-align: middle;
}
.chart {
  height: 1000px;
}
.parent {
  position: relative;
}
.child {
  position: absolute;
  z-index: 99999;
  top: 0;
  left: 0;
}
</style>
