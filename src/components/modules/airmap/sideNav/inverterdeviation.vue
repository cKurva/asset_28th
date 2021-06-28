<template>
  <div>
    <transition name="slide-fade" mode="out-in" :key="currentInverter">
      <div :key="currentDate" >
        <!-- <transition name="slide-fade" mode="out-in">
          <h4 class="text-center sub_title" :key="currentInverter">
            {{ currentInverter }}
          </h4>
        </transition> -->
        <div class="inverterListContainer customScroll">
         <b-form-select
            v-model="select.selected"
            class="project-select"
            @change="inverterPageChange"
          >
            <option
              v-for="(selectOption, indexOpt) in select.options"
              :key="indexOpt"
              :value="selectOption"
            >
              {{ selectOption }}
            </option>
          </b-form-select>

          <transition name="slide-fade" mode="out-in">
            <div :key="currentDate">
              <div
                class="sidenavListItem"
                
                @click="renderDetails(key, index, asset)"
                v-for="(asset, key, index) in inverterdeviationApiData.test[
                this.inverterCurrentPage
              ]"
                :key="index"
              >
                <div>
                  <div v-b-toggle="`dev${index}`" style="color:black">
                  {{ key.toLowerCase() | capitalize }}
                  <i
                    aria-hidden="true"
                    :style="{ color: `rgb(${String(asset.color)})` }"
                    class="fas fa-circle float-right"
                  ></i>
                  </div>
                </div>
                <div>
                  
                </div>

                <b-collapse v-if="Object.values(asset).length >= 1" :id="`dev${index}`">
                    <div class="mt-3">
                       <p><span>Actual : {{asset.actual}}</span> | 
                       <span>Total : {{asset.total}}</span></p>
                    </div>
                 </b-collapse>
                
              </div>
              
            </div>
          </transition>
          <!-- <b-list-group>
            <b-list-group-item
              v-for="(itemL1, keyL1, indexL1) in inverterApiData.invertersData[inverterCurrentPage]"
              :key="keyL1"
            >
              <transition name="slide-fade" mode="out-in">
                <div class="sideBarList" :key="currentInverter">
                  <div v-b-toggle="`${currentInverter}${indexL1}L1`">
                    <span>{{ keyL1 }}</span>
                    <span>
                      <i :class="`fas fa-caret-down`" aria-hidden="true"></i>
                    </span>
                  </div>
                  <b-collapse
                    v-if="Object.keys(itemL1).length > 1"
                    :id="`${currentInverter}${indexL1}L1`"
                    class="mt-2"
                  >
                    <div
                      class="subItem1"
                      style="cursor: pointer;"
                      v-for="(itemL2, keyL2, indexL2) in itemL1"
                      :key="`${keyL2}-${indexL2}`"
                    >
                      <div
                        v-b-toggle="`${currentInverter}${indexL1}${indexL2}L2`"
                      >
                        <span>
                          {{ keyL2 }}
                        </span>
                        <span
                          ><i
                            :class="`fas fa-caret-down`"
                            aria-hidden="true"
                          ></i
                        ></span>
                      </div>
                      <b-collapse
                        v-if="Object.keys(itemL2).length >=1"
                        :id="`${currentInverter}${indexL1}${indexL2}L2`"
                        class="mt-2"
                      >
                        <div
                          class="subItem2"
                          v-for="(itemL3, keyL3, indexL3) in itemL2"
                          :key="`${keyL3}-${indexL3}`"
                          :class="{
                            clickedHighlighter:
                              clickedInverter === keyL2 + keyL3,
                          }"
                          @click="renderDetails(keyL2, keyL3, itemL3)"
                        >
                          <span>
                            {{ keyL3 }}
                          </span>
                        </div>
                      </b-collapse>
                    </div>
                  </b-collapse>
                </div>
              </transition>
            </b-list-group-item>
          </b-list-group> -->
        </div>
        <!--<div class="inverterPaginator">
          <b-pagination
            :total-rows="inverterTotalPages"
            v-model="inverterCurrentPage"
            :per-page="1"
            align="center"
            @change="inverterPageChange"
            size="sm"
          ></b-pagination>
        </div>-->
        <transition name="fade">
          <div v-if="clickedDetailsVisible" class="sideBarDetailsContainer">
            <div class="clickedHeader">
              <span class="clickedTitle">Details</span>
              <span class="closeIcon">
                <i
                  class="icon fa fa-times"
                  aria-hidden="true"
                  aria-controls="sidebar"
                  @click="closeDetails()"
                ></i>
              </span>
            </div>
            <div class="clickedDetails">
            <div class="clickedItemHeader">{{ clickedDetails.kml }}</div>
            
            <b-list-group-item>
              <div v-for="(item3, key3, index3) in clickedDetails.properties"
                      :key="`${key3}-${index3}`">
                    {{key3}} : <span class="float-right"><b>{{item3}}</b></span>
              </div>
               
            </b-list-group-item>
            
          </div>
          </div>
        </transition>
      </div>
    </transition>
    <div class="text-right down_btn">
      <b-button
        pill
         @click="uploadcsv()"
        class="primaryBg upload-csv"
        v-b-tooltip.hover
        title="Upload CSV"
        ><i class="fa fa-upload"></i
      ></b-button>
      <b-dropdown class="download-drop" droptop variant="dark" html='<i class="fa fa-download"></i>'>

        <b-dropdown-item href="javascript:;" @click="downloadInverter()"><i class="fa fa-file-text-o"
        v-b-tooltip.hover
        title="Download CSV"></i> Download CSV</b-dropdown-item>
        <b-dropdown-item href="javascript:;" @click="downloadInverter()"><i class="fa fa-file-excel-o"
        v-b-tooltip.hover
        title="Download Excel"></i> Download Excel</b-dropdown-item>
      </b-dropdown>
    </div>
  </div>
</template>

<script>
var temp1 = ""
export default {
  name: "inverterdeviation",
  props: ["inverterdeviationApiData"],
  data() {
    return {
      clickedDetailsVisible: false,
      clickedDetails: {},
      clickedInverter: null,

      inverterCurrentPage: this.inverterdeviationApiData.inverters[0],
      select: {
        selected: this.inverterdeviationApiData.inverters[0],
        options: this.inverterdeviationApiData.inverters,
      },
    };
  },
  computed: {
    currentDate: function() {
      let currentDate = this.$store.state.airmapStore.currentDate;
      return currentDate;
    },
    // inverterTotalPages: function() {
    //   let inverterPages = this.inverterdeviationApiData.inverters.length 

    //   return inverterPages ? inverterPages : 0;
    // },
    kmlBaseUrl: function () {
      let kmlBaseUrl = this.$store.state.projectStore.projectProperties.kml;
      return kmlBaseUrl ? kmlBaseUrl : "";
    },
    currentInverter: function() {
      let currentInverter = this.inverterCurrentPage
            console.log(this.inverterCurrentPage)  

      this.$root.$emit("kmlClicked", {
        url:
          `${this.kmlBaseUrl}` + "inverter/" + `${currentInverter}` + "/gb.kml",
        kml_type: "gb",
      });
            
      return currentInverter ? currentInverter : {};
    },
  },

  methods: {
    uploadcsv(){
      this.$bvModal.show("csvInvUploadModel");
    },
    componentToHex(c) {
      var hex = c.toString(16);
      return hex.length == 1 ? "0" + hex : hex;
    },
    rgbToHex(r, g, b) {
      return "#" + this.componentToHex(r) + this.componentToHex(g) + this.componentToHex(b);
    },
    renderDetails(keyL2, keyL3, value) {

      this.clickedDetailsVisible = !this.clickedDetailsVisible;
      if(this.clickedDetailsVisible == false){
        if(keyL3 == temp1){
        this.clickedDetails = {};
        this.$root.$emit("removeKml");
      }
      if(keyL3 !== temp1){
        this.clickedDetailsVisible = !this.clickedDetailsVisible;
          this.clickedInverter = keyL2 + keyL3;
      this.clickedDetails = value;
      
      // this.$store.dispatch("airmapStore/getKmlAPI");
      // const rgb = value.color.substring(4, value.color.length-1)
      //    .replace(/ /g, '')
      //    .split(',');
        const r = parseInt(value.color[0])
        const g = parseInt(value.color[1])
        const b = parseInt(value.color[2])
      let hex_color =  this.rgbToHex(r,g,b)
      if (value) {
        if (value.kml) {
          this.$root.$emit("kmlClicked", {
            url:
              `${this.kmlBaseUrl}` +
              "inverter/" +
              `${this.currentInverter}` +
              "/gb.kml",
            // color: String(value.color),
          });
          this.$root.$emit("kmlClicked", {
            url:
              `${this.kmlBaseUrl}` +
              "inverter/" +
              `${this.currentInverter}/` +
              `${value.kml}.kml`,
            color: String(value.color),
            hex: hex_color,
          });
        }
      }
       temp1 = keyL3
      }
      }
      else{
        this.clickedInverter = keyL2 + keyL3;
      this.clickedDetails = value;
      
      // this.$store.dispatch("airmapStore/getKmlAPI");
      // const rgb = value.color.substring(4, value.color.length-1)
      //    .replace(/ /g, '')
      //    .split(',');
        const r = parseInt(value.color[0])
        const g = parseInt(value.color[1])
        const b = parseInt(value.color[2])
      let hex_color =  this.rgbToHex(r,g,b)
      if (value) {
        if (value.kml) {
          this.$root.$emit("kmlClicked", {
            url:
              `${this.kmlBaseUrl}` +
              "inverter/" +
              `${this.currentInverter}` +
              "/gb.kml",
            // color: String(value.color),
          });
          this.$root.$emit("kmlClicked", {
            url:
              `${this.kmlBaseUrl}` +
              "inverter/" +
              `${this.currentInverter}/` +
              `${value.kml}.kml`,
            color: String(value.color),
            hex: hex_color,
          });
        }
      }
       temp1 = keyL3
      }
      /* this.clickedInverter = keyL2 + keyL3;
      this.clickedDetails = value;
      
      // this.$store.dispatch("airmapStore/getKmlAPI");
      // const rgb = value.color.substring(4, value.color.length-1)
      //    .replace(/ /g, '')
      //    .split(',');
        const r = parseInt(value.color[0])
        const g = parseInt(value.color[1])
        const b = parseInt(value.color[2])
      let hex_color =  this.rgbToHex(r,g,b)
      if (value) {
        if (value.kml) {
          this.$root.$emit("kmlClicked", {
            url:
              `${this.kmlBaseUrl}` +
              "inverter/" +
              `${this.currentInverter}` +
              "/gb.kml",
            // color: String(value.color),
          });
          this.$root.$emit("kmlClicked", {
            url:
              `${this.kmlBaseUrl}` +
              "inverter/" +
              `${this.currentInverter}/` +
              `${value.kml}.kml`,
            color: String(value.color),
            hex: hex_color,
          });
        }
      } */
    },
    closeDetails() {
      this.clickedDetails = {};
      this.clickedDetailsVisible = false;
            this.$root.$emit("removeKml");

    },
    inverterPageChange(page) {
      // this.clickedInverter = "";
      this.inverterCurrentPage = page;
    },
    downloadInverter() {
      console.log("Hii")
      this.$store.dispatch(
        "airmapStore/downloadCompileApi",
        "downloadinverter"
      );
      
    },
    
  },
  destroyed() {
    this.$root.$emit("removeKml");
  },
};
</script>

<style >
.inverterListContainer {
   max-height: calc(100vh - 280px);
  overflow: auto;
      box-shadow: 0px 0px 10px #d8d5d5;
    margin: 15px;
}
.sideBarList {
  display: flex;
  flex-direction: column;
  text-transform: capitalize;
  text-align: left;
  overflow-y: auto;
}



.not-collapsed + div {
 
  margin: 0 !important;
}

.subItem1 {
 
  overflow: auto;
}
.subItem2 {
  padding: 10px;
  text-align: left;
}
.subItem2:hover {
 
}
.clickedHighlighter {
  background-color: var(--primaryBg);
 
}
.inverterPaginator {
  margin-top: 20px;
}
.sideBarDetailsContainer {
  position: absolute;
  left: 350px !important;
  bottom: 100px !important;
  min-width: 450px !important;
  
  min-height: 80px  !important;
  color: var(--dark);
  background-color: var(--light);
  box-shadow: 1px 1px var(--shadow);
  border-radius: 10px;
  transition: all 1s ease-in;
}
.sideBarDetailsContainer div:last-child {
  border-radius: 0 0 10px 10px;
}
.sideBarDetailsContainer .list-group-item {
    padding: 10px !important;
}
.clickedHeader {
  display: flex;
  justify-content: space-between;
  background-color: var(--primaryBg);
  color: var(--light);
  
}
.clickedTitle {
  font-size: 1.3em;
  padding: 10px;
}
.closeIcon {
  padding: 10px;
  transition: all 0.2s;
}
.closeIcon:hover {
  transform: scale(1.2);
}
.clickedItemHeader {
  font-weight: bold;
  padding: 15px;
  border-bottom: 1.5px solid rgba(0, 0, 0, 0.125);
}

.slide-fade-enter-active {
  transition: all 0.3s ease;
}
.slide-fade-leave-active {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active for <2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
.sideBarList > div:first-child {
  display: flex;
  justify-content: space-between;
  padding: 10px 20px;
}
.list-group-item {
  padding: 0 !important;
}

</style>
