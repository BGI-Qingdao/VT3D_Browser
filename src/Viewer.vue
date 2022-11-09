<template>
<div>
    <!---------------------------- All floating items begin --------------------------------------------------- -->
    <div  style="height=1px;">
      <el-dialog title="Color picker" style="width:500px;" :visible.sync="drawer" direction="ltr" :before-close="OnDrawerClose">
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
      <el-dialog title="MIP image" width="95%"  style="width:1000px;" :visible.sync="mip_drawer" direction="ltr">
          <img :src=fish_url >
      </el-dialog>
    </div>
    <!---------------------------- All floating items end --------------------------------------------------- -->
    <!---------------------------- Main windown begin --------------------------------------------------- -->
    <el-row>
      <el-button class="floatsetting" v-show="!display_setting" type="primary" icon="el-icon-setting"  @click="span_setting" >{{setting_title}}</el-button>
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
                         <el-row style="margin-top:3px;margin-bottom:2px">
                             <el-col :span="8" >
                                <span  class='mspan'>Data:</span>
                             </el-col>
                             <el-col :span="16" >
                                 <el-select v-model="curr_sample" placeholder="curr_sample" @change="OnChangeSample">
                                   <el-option
                                     v-for="item in all_sample"
                                     :key="item"
                                     :label="item"
                                     :value="item">
                                   </el-option>
                                 </el-select>
                             </el-col>
                         </el-row>
                         <el-row style="margin-top:3px;margin-bottom:2px">
                             <el-col :span="8" >
                                <span  class='mspan'>Corrdinate:</span>
                             </el-col>
                             <el-col :span="16" >
                                 <el-select  v-model="curr_coord" placeholder="curr_coord" @change="OnChangeCoord">
                                   <el-option
                                     v-for="item in coord_array"
                                     :key="item"
                                     :label="item"
                                     :value="item">
                                   </el-option>
                                 </el-select>
                             </el-col>
                         </el-row>
                         <!--
                         <el-row style="margin-top:3px;margin-bottom:2px">
                             <el-col :span="8" >
                                <span  class='mspan'>blendMode:</span>
                             </el-col>
                             <el-col :span="16" >
                                 <el-select  v-model="curr_blender" placeholder="curr_blender" @change="OnChangeBlender">
                                   <el-option key="source-over" label="alpha" value="source-over"></el-option>
                                   <el-option key="lighter" label="lighter" value="lighter"></el-option>
                                 </el-select>
                             </el-col>
                         </el-row>
                         -->
                         <!-- ----------Choose mode end --------------------------------------------------------------- -->
                     </div>
                     <!-- ----------The Baisc settings end -------------------------------------------------------------- -->
                  <el-collapse v-model='collapseName'>
                     <!-- ----------The Baisc settings begin --------------------------------------------------------------- -->
                      <el-collapse-item :title="mode_title" name="1" >
                      <!-- ----------mode setting begin --------------------------------------------------------------- -->
                         <!-- ----------cell type mode begin--------------------------------------------------------------- -->
                         <div align="center"  v-show="is_ct_mode" style="margin:3px;   border: 3px solid #ccc;">
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
                                  @selection-change="handleSelectionChange" @row-click="getRowCelltype">
                                    <el-table-column :reserve-selection="true" type="selection" ></el-table-column>
                                    <el-table-column property="Celltype" label="Cell Type" > </el-table-column>
                                    <el-table-column label="Display" >
                                      <el-button @click="showColorPalette">color</el-button>
                                      <!-- <template slot-scope="scope"> -->
                                        <!-- <el-button size="mini" type="primary" plain @click ="changeColor">color</el-button> -->
                                      <!-- </template> -->
                                    </el-table-column>
                                </el-table>
                                <el-pagination layout="total,sizes" 
                                  :total="this.tableDataClusters.length" :current-page="currentPage" 
                                  @current-change="handleCurrentChange" @size-change="handleSizeChange" >
                                </el-pagination>
                                <el-pagination layout="prev, jumper, next" 
                                  :page-sizes="[10,20]" :page-size="pageSize" :current-page.sync="currentPage">
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
                         <div align="center"  v-show="is_ge_mode" style="margin:3px;   border: 3px solid #ccc;">
                           <!--
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="8" >
                                   <span  class='mspan'>Type:</span>
                                </el-col>
                                <el-col :span="16" >
                                    <el-select  v-model="curr_genename_system" placeholder="curr_genename_system" @change="OnGeneNameSystemChange">
                                      <el-option
                                        v-for="item in genename_array"
                                        :key="item"
                                        :label="item"
                                        :value="item">
                                      </el-option>
                                    </el-select>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="8" >
                                   <span  class='mspan'>Normed:</span>
                                </el-col>
                                <el-col :span="16" >
                                    <el-select  v-model="curr_norm" placeholder="curr_norm" @change="OnGeneNormChange">
                                      <el-option
                                        v-for="item in normed_array"
                                        :key="item"
                                        :label="item"
                                        :value="item">
                                      </el-option>
                                    </el-select>
                                </el-col>
                            </el-row>
                            <hr class="dhr">
                           -->
                            <div align="left" style="margin-left:10px;">
                                <el-row style="margin-top:1px;margin-bottom:1px">
                                    <span class='mspan'>Please choose an DEG:</span>
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
                                        :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                                        :highlight-current-row='true'
                                        stripe
                                        @row-dblclick='handleRow'>
                                            <el-table-column prop='smesg' label='SMESG'></el-table-column>
                                            <el-table-column prop='smed' label='SMED'></el-table-column>
                                            <el-table-column prop='gene_name' label='Name'></el-table-column>
                                        </el-table>
                                        <!-- gene table end-->
                                    </el-col>
                                </el-row>
                                <el-row style="margin-top:3px;margin-bottom:2px">
                                        <el-pagination layout="total,prev,next,jumper"
                                        :total="this.tableData.length"
                                        :current-page="currentPage"
                                        @current-change="handleCurrentChange" @size-change="handleSizeChange"
                                        :page-sizes="[3,5,10]" :page-size="pageSize" :current-page.sync="currentPage">
                                        </el-pagination>
                                </el-row>

                                <!--
                                <el-row style="margin-top:3px;margin-bottom:2px">
                                    <el-col :span="8" >
                                        <span class='mspan'>Min Exp:</span>
                                    </el-col>
                                    <el-col :span="15" >
                                        <el-slider  v-model="smallestExpression" :step="0.5" :min="0" :max="curr_max_exp" @change="changeExpression" show-stops>
                                        </el-slider>
                                    </el-col>
                                </el-row>
                                <el-row style="margin-top:3px;margin-bottom:2px">
                                    <el-col :span="8" >
                                        <span class='mspan'>Max Exp:</span>
                                    </el-col>
                                    <el-col :span="15" >
                                        <el-slider  v-model="largestExpression" :step="0.5" :min="0" :max="curr_max_exp" @change="changeExpression" show-stops>
                                        </el-slider>
                                    </el-col>
                                </el-row>
                                -->
                            </div>
                            <el-row style="margin-top:2px;margin-bottom:3px">
                                <el-button type="success"  @click.native="OnOpenFISH">MIP image</el-button>
                            </el-row>
                         </div>
                         <!-- ----------gene selection mode start--------------------------------------------------------------- -->
                         <!-- ----------digital in situ mode start--------------------------------------------------------------- -->
                         <div align="center"  v-show="is_gc_mode" style="margin:3px; border: 3px solid #ccc;">
                            <!--
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="8" >
                                   <span  class='mspan'>Type:</span>
                                </el-col>
                                <el-col :span="16" >
                                    <el-select  v-model="curr_genename_system" placeholder="curr_genename_system" @change="OnGeneNameSystemChange">
                                      <el-option
                                        v-for="item in genename_array"
                                        :key="item"
                                        :label="item"
                                        :value="item">
                                      </el-option>
                                    </el-select>
                                </el-col>
                            </el-row>
                            <el-row style="margin-top:3px;margin-bottom:2px">
                                <el-col :span="8" >
                                   <span  class='mspan'>Normed:</span>
                                </el-col>
                                <el-col :span="16" >
                                    <el-select  v-model="curr_norm" placeholder="curr_norm" @change="OnGeneNormChange">
                                      <el-option
                                        v-for="item in normed_array"
                                        :key="item"
                                        :label="item"
                                        :value="item">
                                      </el-option>
                                    </el-select>
                                </el-col>
                            </el-row>
                            <hr class="dhr">
                            -->
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
                      <el-collapse-item title="ROI details:" name="2">
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
                          </div>
                         <!-- ----------roi setting end--------------------------------------------------------------- -->
                      </el-collapse-item>
                      <el-collapse-item title="Display details:" name="3">
                          <!-- ----------display setting begin --------------------------------------------------------------- -->
                          <div align="center"  style="margin:3px;  border: 3px solid #ccc;">
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
                                         :step="1" :min="1" :max="5" @change="refresh" show-stops>
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
        </div>
        <!-- end of the main window -->
      </el-col>
      <!-- ----------The 3D display grid end --------------------------------------------------------------- -->
    </el-row>
    <!---------------------------- Main windown end--------------------------------------------------- -->
</div>
</template>

<script>
//import packages ------------------------------------
import $ from 'jquery';
import * as echarts from 'echarts';
import 'echarts-gl';
import VChart, { THEME_KEY } from "vue-echarts";
import { Draggable } from 'draggable-vue-directive';
import vdr from 'vue-draggable-resizable-gorkys'
import { Sketch } from 'vue-color'
// axis limitation conf --------------------------------
var idvd_conf_corrected = require('../confs/individual_corrected.js');
var idvd_conf_rotate = require('../confs/individual_rotated.js');
// cell type details conf --------------------------------
var CT_CONFS = require('../confs/individual_conf.js')
// cell type legend and the color conf --------------------------------
var COLOR_ALL = require('../confs/discret_color.js');
var celltype_legend = require('../confs/CellType.js');
var COLOR_default = COLOR_ALL.default_colors;
var COLOR_9 = COLOR_ALL.color_9;
// cell type legend and the color conf --------------------------------
// URL manager
var url_manager = require('../confs/urls.js');
var CT_URL = url_manager.ANNO_URL;
var CP_URL = url_manager.MESH_URL;
// Gene ID Mapping table
var gene_id_url = url_manager.GENETABLE_URL;

export default {
components: {
  VChart,
  vdr,
  'sketch-picker': Sketch
},
directives: {
  Draggable,
},
data() {
    return {
      // setting basics --------------------------------------
      display_setting: true,
      setting_title :'Hide settings',
      setting_w: 4,
      display_w: 20,
      // collapse details   
      collapseName: '1',
      // valid mode:
      all_modes: [
          'CellTypes',
          'GeneExpression',
          'Digital_in_situ',
      ],
      curr_mode : 'CellTypes',
      // mode tags
      is_ct_mode: true,
      is_ge_mode: false,
      is_gc_mode: false,
      // valid samples:
      all_sample: [
           'All17Sample',
           'WT',
           '0hpa1',
           '0hpa2',
           '12hpa1',
           '12hpa2',
           '36hpa1',
           '36hpa2',
           '3dpa1',
           '3dpa2',
           '5dpa1',
           '5dpa2',
           '7dpa1',
           '7dpa2',
           '10dpa1',
           '10dpa2',
           '14dpa1',
           '14dpa2',
      ],
      curr_sample: 'WT',
      mode_title:'CellType setting',
      //------------body posture confs begin------
      // coordinate tag
      coord_tag : 'adjusted',
      //------------body posture confs end ------
      
      //------------color legends confs begin------
      // test color 
      COLOR_9 : COLOR_9,
      COLOR_default :COLOR_default,
      COLOR_ALL2: COLOR_default,
      current_color_all: null,
      currentCellID: '',
      color: '',
      //------------color legends confs end------
      //------------drag element confs begin------
      // test drag
      //------------drag element confs end------
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
      black_background:true,
      draw_legends:true,
      draw_grids:false,
      draw_box :true,
      adjusted_posture:true,
      symbolSize:2,
      symbolAlpha:1.0,
      // drawing details conf end----------
      // echarts option begin -------------
      chartWidth:'100%',
      model_only:true,
      data_valid:false,
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
      //------------data selection for cell type begin ------
      // curr selection
      curr_name : null,
      curr_rs : null,
      final_clusters: [],
      tmp_cluster_num: 0,
      all_clusters: 0,
      saved_clusters:[], // the selection cache
      selected_rs_index:"0",
      coord_array : [
            'Raw posture',
            'Adjusted posture',
      ],
      anno_array : CT_CONFS.label_WT.anno,
      curr_anno : null,
      curr_coord : null,
      curr_blender: "source-over",
      rawdata:null,
      jsondata : null,
      //------------data selection for cell type end ------
      //------------cell type selection begin ------
      isCTCHidden:true,
      tableDataClusters: [],
      height:'250px',
      pageSize:5,
      currentPage:1,
      min_cluster_number: 0,
      max_cluster_number: 100,
      drawer:false,
      //------------cell type selection end------

      //------------gene expression selection start------
      genename_array : [
         'SMED',
         'SMESG'
      ],
      normed_array : [
         'scaled',
         'sct_transformed',
      ],
      // 2022-10-10, gene table data
      tableData: [],
      allTableData: [],
      pageSize:5,
      currentPage:1,
      // end
      curr_selected_gene : '',
      input_gene_id : '',
      curr_gene : "",
      curr_genename_system : 'SMESG',
      curr_norm : 'sct_transformed',
      curr_gene_url : url_manager.GENE_URL.SMESG.sct_transformed,
      gene_json_raw : null,
      gene_json_data : null,
      //smallestExpression:0,
      //largestExpression:10,
      curr_max_exp:6,
      fish_url:null,
      mip_drawer:false,
      //------------gene expression selection end------

      //------------channel selection start------
      input_gene_red:     'SMESG000066416.1',
      input_gene_green:   'SMESG000008070.1',
      input_gene_blue:    'SMESG000068721.1',
      input_gene_gray:    'SMESG000033795.1',
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
          red:"#ff0000",
          green:"#00ff00",
          blue:"#0000ff",
          gray:"#888888",
          cyan:"#00ffff",
          magenta:"#ff00fff",
          yellow:"#ffff00"
      },
      min_cutoff : 3,
      //------------channel selection end------
      //------------model data : mesh begin ------
      mesh_json:null,
      mesh_conf : { 
        names:    ['Body','Neural','Gut','Pharynx'],
        legends : ['Body_mesh','Neural_mesh','Gut_mesh','Pharynx_mesh'],
        //colors :  ['#cc6600','#ffff00','#ff0000','#00ff00'],
        colors :  ['#7d6a43','#b6b6b6','#a871a7','#609148'],
        opacity:  [ 0.3, 0.55, 0.55, 0.55],
      },
      //------------model data : mesh begin ------
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
 
    //----------- gene select functions start -------------//
    update_gene_url() {
        this.curr_gene_url = url_manager.GENE_URL[this.curr_genename_system][this.curr_norm];
    },
    OnGeneNameSystemChange() {
        this.update_gene_url();
        if( this.curr_mode == "GeneExpression" ) {
            this.resetDefaultGene();
            this.resetGene();
        } else {
            this.resetDefaultChannelGene();
            this.resetChannel();
        }
    },
    resetDefaultGene() {

    },
    resetGene() {
        this.gene_json_raw = null;
        this.gene_json_data = null;

        if(this.curr_selected_gene != "") {
            this.curr_gene = this.curr_selected_gene;
        } else if (this.input_gene_id != "") {
            this.curr_gene = this.input_gene_id;
        }
        this.refreshGene(this.curr_gene,true);
    },
    OnGeneNormChange() {
        this.update_gene_url();
        if( this.curr_mode == "GeneExpression" ) {
            this.resetGene();
        } else {
            this.resetChannel();
        }
    },
    loadGeneTable(){
        if ( this.allTableData.length <1 ) {
            // 2022-10-10 liyao: load gene id mapping table
            var self = this;
            $.getJSON(gene_id_url, function(_data){
                self.tableData = _data;
                console.log('getOption');
                console.log('loadGeneIDTable');
                console.log(self.tableData);
                self.allTableData = _data;
                //self.selectSample(self.currentSpecies);
            });
        }
    },
    updateTable(){
        // 2022-10-10 search gene
        console.log('updateTable');
        var new_tableData = [];
        var arrayLength = this.allTableData.length;
        if (this.curr_genename_system == 'SMESG'){
            for (var i = 0; i < arrayLength; i++) {
                if (this.allTableData[i]['smesg'].includes(this.input_gene_id)){
                    new_tableData.push(this.allTableData[i]);
                }else if (this.allTableData[i]['smed'].includes(this.input_gene_id)){
                    new_tableData.push(this.allTableData[i]);
                }else if (this.allTableData[i]['gene_name'].includes(this.input_gene_id)){
                    new_tableData.push(this.allTableData[i]);
                }
            }
        }else if (this.curr_genename_system == 'SMED') {
            for (var i = 0; i < arrayLength; i++) {
                if (this.allTableData[i]['smed'].includes(this.input_gene_id)){
                    new_tableData.push(this.allTableData[i]);
                }
            }
        }
        this.tableData = new_tableData;
    },
    handleRow(row,event,column){
        if (this.curr_genename_system == 'SMESG'){
            this.input_gene_id = row.smesg;
            //this.curr_selected_gene = row.smesg;
        }else if (this.curr_genename_system == 'SMED') {
            this.input_gene_id = row.smed;
            //this.curr_selected_gene = row.smed;
        }
        console.log(this.input_gene_id);
        this.updateTable();
        this.refreshGene(this.input_gene_id,true);
    },
    UseGeneID() {
        this.updateTable();
    },
    get_curr_gene_url(gene_name) {
        return this.curr_gene_url+"/"+this.curr_name+"/"+gene_name+".json";
    },
    changeExpression(){
        this.updateJsonData();
        this.update_option();
    },
    OnOpenFISH(){
        var base_url =  url_manager.PSEUDO_FISH_URL[this.curr_genename_system];
        this.fish_url = base_url + '/' + this.curr_name+'/'+this.curr_name+'_'+this.curr_gene+".green.MIR.png";
        console.log(this.fish_url)
        this.mip_drawer = true;
    },
    refreshGene(gname, force) {
        if ( gname == null || gname == "" ) return; 
        if (this.curr_gene != gname || force ) {
            this.gene_json_raw = null;
            this.gene_json_data = null;
            this.update_option_deep();
            var self = this;
            this.curr_gene = gname;
            var used_url = this.get_curr_gene_url(this.curr_gene);
            //var used_url = this.curr_gene_url+"/"+this.curr_name+"/"+this.curr_gene+".json";
            $.getJSON(used_url,function(_data) {
              self.setGeneData(_data);
              self.update_option_deep();
            });
        }
        console.log(this.input_gene_id);
        if (this.input_gene_id != ''){

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
            gene_xyz.push( [curr_item[0],curr_item[1],curr_item[2],curr_item[3]]);
        }
        //this.smallestExpression = this.curr_max_exp/5*3 ;
        //this.largestExpression = this.curr_max_exp;
        console.log(this.curr_max_exp);
        this.gene_json_raw= gene_xyz;
        this.updateJsonData();
        //this.gene_json_data = gene_xyz ;
    },
    //----------- gene select functions end -------------//

    //----------- color palette start -------------//
    showColorPalette(){
      this.drawer = true; 
    },
    colorValueChange (val) {
     this.color = val.hex;
    },
    applyColor(){
      // change color when click row
      // only apply changes when click button
      this.current_color_all = this.COLOR_ALL2;
      this.current_color_all[this.currentCellID] = this.color;
      this.option=this.getOption()  ;
    },
    closeColor(){
      this.drawer = false;
    },
    //-------------color palette ends------------------//
    // ------------------ functions for cell type table begin -----------------
    OnDrawerClose(done){
        this.drawer= false;
        done();
    },
    openCTC(){
        this.isCTCHidden = !this.isCTCHidden;
        this.drawer = !this.drawer;
        console.log(this.drawer);
    },
    getRowCelltype(row){
      // 1. get row id when click on row (except for selection box)
      this.currentCellID = row.ID;
      console.log('getrow '+this.currentCellID);
      return row.ID;
    },
    getRowKey (row) {
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
      this.option=this.getOption();
    },
    clearSelect () {
      this.currentCellID = '';
      this.$refs.clusterTable.clearSelection();
    },
    applyStatus() {
      var self = this;
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
    // ------------------ basic conf functions begin----------------------
    OnChangeMode(){
        this.mode_title = this.curr_mode+" setting";
        console.log(this.curr_mode)
        if( this.curr_mode == "CellTypes") {
            this.is_ct_mode = true;
            this.is_gc_mode = false;
            this.is_ge_mode = false;
            this.coord_array = [
                'Raw posture',
                'Adjusted posture',
            ];
        }else if ( this.curr_mode == "GeneExpression" ) {
            this.is_ct_mode = false;
            this.is_gc_mode = false;
            this.is_ge_mode = true;
            this.coord_array = [
                'Adjusted posture',
            ];
            this.loadGeneTable();
        } else {
            this.is_ct_mode = false;
            this.is_gc_mode = true;
            this.is_ge_mode = false;
            this.coord_array = [
                'Adjusted posture',
            ];
            this.loadGeneTable();
        }
        this.OnChangeSample();
    },
    setCoordTag(){
        if(this.curr_coord == "Raw posture" || this.curr_coord == "Adjusted posture" ) {
            if (this.curr_coord == "Raw posture"){
                this.coord_tag = "raw";
            } else {
                this.coord_tag = "adjusted";
            }
        }
    },
    resetSampleConf(){
        this.resetCellTypeSampleConf();
        this.setCoordTag();
    },

    OnChangeSample(){
        this.curr_name = this.curr_sample;
        this.resetSampleConf();
        //deep clean buffer
        this.cleanMesh();
        this.cleanBuffer();
        this.update_option_deep();
        // reset mesh and ROI data
        this.resetROIdata();
        this.resetMesh();
        if(this.curr_mode == "CellTypes"){
            this.resetAnno();
        } else if (this.curr_mode == "GeneExpression"){
            this.resetGene();
        } else {
            this.resetChannel();
        }
    },
    // ------------------ basic conf functions end----------------------
    // --------------cell type detail confs begin ----------------------------
    resetCellTypeSampleConf(){
        this.anno_array = CT_CONFS["label_"+this.curr_sample].anno;
        this.curr_anno = this.anno_array[0];
        this.curr_coord = this.coord_array[0];
    },
    OnChangeAnno(){
        this.cleanBuffer();
        this.resetAnno();
        this.update_option_deep();
    },
    OnChangeBlender(){
        this.update_option();
    },
    OnChangeCoord(){
        //deep clean buffer
        this.cleanMesh();
        this.cleanBuffer();
        this.update_option_deep();
        // reset mesh and ROI data
        this.setCoordTag();
        this.resetROIdata();
        this.resetMesh();
        if(this.curr_mode == "CellTypes"){
            this.resetAnno();
        }
    },
    // --------------cell type detail confs end ----------------------------
    //-------------3d box conf start-------------------//
    getXMin(){
      if( this.coord_tag == "raw" ) 
          return idvd_conf_rotate['label_'+this.curr_name].xmin/10;
      else 
          return idvd_conf_corrected['label_'+this.curr_name].xmin/10;
    },
    getXMax(){
      if( this.coord_tag == "raw" ) 
          return idvd_conf_rotate['label_'+this.curr_name].xmax/10;
      else 
          return idvd_conf_corrected['label_'+this.curr_name].xmax/10;
    },
    getYMin(){
      if( this.coord_tag == "raw" ) 
          return idvd_conf_rotate['label_'+this.curr_name].ymin/10;
      else 
          return idvd_conf_corrected['label_'+this.curr_name].ymin/10;
    },
    getYMax(){
      if( this.coord_tag == "raw" ) 
          return idvd_conf_rotate['label_'+this.curr_name].ymax/10;
      else 
          return idvd_conf_corrected['label_'+this.curr_name].ymax/10;
    },
    getZMin(){
      if( this.coord_tag == "raw" ) 
          return idvd_conf_rotate['label_'+this.curr_name].zmin/10;
      else 
          return idvd_conf_corrected['label_'+this.curr_name].zmin/10;
    },
    getZMax(){
      if( this.coord_tag == "raw" ) 
          return idvd_conf_rotate['label_'+this.curr_name].zmax/10;
      else 
          return idvd_conf_corrected['label_'+this.curr_name].zmax/10;
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
            var used_url = this.get_curr_gene_url(input_gene);
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
            gene_xyz.push( [curr_item[0],curr_item[1],curr_item[2],curr_item[3]]);
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
    },
    resetMesh() {
      this.cleanMesh();
      // loading new mesh
      var self = this;
      var used_url = '';
      if( this.coord_tag == "raw" ) {
        used_url = CP_URL+"/"+this.curr_name+"_mesh.rotate.json";
      } else {
        used_url = CP_URL+"/"+this.curr_name+"_mesh.apdv.json";
      }
      console.log(used_url);
      $.getJSON(used_url,function(_data) {
        console.log("mesh loaded");
        self.setMeshData(_data);
        self.update_option_deep();
      });
    },
    setMeshData(_data) {
        this.mesh_json = {};
        this.mesh_json['Body'] = {}
        this.mesh_json['Body']['xyz'] = _data[0][0];
        this.mesh_json['Body']['ijk'] = _data[0][1];
        this.mesh_json['Neural'] = {}
        this.mesh_json['Neural']['xyz'] = _data[1][0];
        this.mesh_json['Neural']['ijk'] = _data[1][1];
        this.mesh_json['Gut'] = {}
        this.mesh_json['Gut']['xyz'] = _data[2][0];
        this.mesh_json['Gut']['ijk'] = _data[2][1];
        if(_data.length>3){
            this.mesh_json['Pharynx'] = {}
            this.mesh_json['Pharynx']['xyz'] = _data[3][0];
            this.mesh_json['Pharynx']['ijk'] = _data[3][1];
        }
    },
    //-------------mesh managerment end -------------------//
    //-------------cell type data managerment start -------------------//
    cleanBuffer(){
      this.jsondata = null ;
      this.rawdata = null;
    },
    resetAnno(){
      var used_url="";
      this.COLOR_ALL2 = this.COLOR_default;
      if(this.curr_anno == "Single Cell WT lineage" ){
          this.curr_rs = "SA_anno";
          this.COLOR_ALL2 = this.COLOR_9;
      } else if ( this.curr_anno == "Single Cell WT cluster"){
          this.curr_rs = "SA_cluster";
      } else if ( this.curr_anno == "Single Cell lineage"){
          this.curr_rs = "major_anno";
      } else if ( this.curr_anno == "Single Cell cluster"){
          this.curr_rs = "major_celltype";
      } else if ( this.curr_anno == "Single Cell sub cluster"){
          this.curr_rs = "sc_subcluster";
      } else if ( this.curr_anno == "SPC lineage"){
          this.curr_rs = "nc_cluster36";
      }
      if(this.curr_coord == "Raw posture" || this.curr_coord == "Adjusted posture" ) {
          used_url = CT_URL+"/"+this.curr_name+"/"+this.curr_rs+"."+this.coord_tag+".json";
      } else {

      }
      var data_tag = this.curr_rs;
      var self = this;
      $.getJSON(used_url,function(_data) {
        console.log("loaded");
        self.setAnnoData(_data,data_tag);
        self.update_option_deep();
      });
    },
    setAnnoData(_data,data_tag){
      console.log('json loaded');
      var curr_draw_datas= [];
      var total_cluster_number = celltype_legend[data_tag].LegendsNum;
      this.final_clusters = new Array(total_cluster_number).fill(0);
      this.all_clusters = total_cluster_number;
      // ---------- iterate through clusters setting (short)
      for(var i = 0; i<=total_cluster_number; i++ ){
          curr_draw_datas.push([]);
      }
      // --------- iterate through real data (long)
      for(var j=0 ; j< _data.length; j++){
        var curr_item = _data[j];
        //console.log(curr_item);
        curr_draw_datas[parseInt(curr_item[3])].push([curr_item[0],curr_item[1],curr_item[2]]);
      } // end of for _data
      // -------- mark empty group
      for (var i = 0; i < total_cluster_number; i++){
        if (curr_draw_datas[i].length == 0) {
          this.final_clusters[i] = 0;
        }else{
          this.final_clusters[i] = 1;
        }
      }
      var new_tableDataClusters = [];
      for (var i=0; i < this.all_clusters; i++){
          new_tableDataClusters.push({ID: i, Celltype:celltype_legend[data_tag].Legends[i] });
      }
      this.tableDataClusters = new_tableDataClusters;
      //console.log(this.tableDataClusters);
      this.rawdata = curr_draw_datas;
      this.jsondata = curr_draw_datas;
    },
    //-------------cell type data managerment end-------------------//
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
            var curr_opacity = this.mesh_conf.opacity[i];
            if ( this.mesh_json[curr_name]['xyz'].length == 0 ) 
                continue;
            //console.log('curr_legend_name');
            legend_list.push(curr_legend_name);
            if ( i == 0 ){
                legend_show[curr_legend_name]=false;
            }else {
                legend_show[curr_legend_name]=true;
            }
            var one_series = {
                name : curr_legend_name,
                type : 'surface',
                data: this.mesh_json[curr_name]['xyz'],
                isMesh :true,
                //zlevel: -10,
                shading:'lambert',
                dataShape:[2,3],
                indices : this.mesh_json[curr_name]['ijk'],
                color: curr_color,
                borderWidth :1,
                opacity:curr_opacity,
                silent:true,
            };
            series_list.push(one_series);
          }
          var ret = {
              series_list: series_list,
              legend_list : legend_list,
              legend_show:legend_show,
          };
          return ret ;
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
            dimensions: [ 'AP','ML','DV' ,'exp'],
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
          var curr_legend_name = celltype_legend[this.curr_rs].Legends[i];
          var the_data = curr_draw_datas[i];
          if(this.final_clusters[i] == 0 || the_data.length==0){
            legend_show[curr_legend_name] = false;
          } else {
            legend_show[curr_legend_name] = true;
          }
          legend_list.push(curr_legend_name);
          //curr_color = COLOR_ALL[i];
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
        //if( max10 == true ){
        //  used_xmin = -10; used_xmax = 10; 
        //  used_ymin = -10; used_ymax = 10; 
        //  used_zmin = -10; used_zmax = 10; 
        //  boxWidth = 60; boxHeight = 60; boxDepth = 60;
        //}
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
              saveAsImage: {},
            }
          },
          tooltip: {},
          xAxis3D: {
            name: 'AP',
            scale:1,
            type: 'value',
            min: used_xmin,
            max: used_xmax,
          },
          yAxis3D: {
            name: 'ML',
            scale:1,
            type: 'value',
            min: used_ymin,
            max: used_ymax,
          },
          zAxis3D: {
            name: 'DV',
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
                 intensity: 3,
                 quality: 'high'
              },
              ambientCubemap: {
                 exposure: 0,
                 diffuseIntensity: 10
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
              // autoRotate: true,
              //projection: 'orthographic'
              projection: 'perspective'
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
    this.OnChangeSample();
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
  height: 800px;
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
