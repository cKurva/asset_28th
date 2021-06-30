<template>
  <div>
    <div class="summaryListContainer customScroll">
      <b-list-group>
        <b-list-group-item
          v-for="(itemL1, keyL1, indexL1) in summaryData"
          :key="indexL1"
        >
          <transition name="slide-fade" mode="out-in">
            <div class="sideBarList" :key="currentDate">
              <div v-b-toggle="`summary${indexL1}L1`">
                <span
                  ><i
                    @click="lightning(itemL1,indexL1)"
                    :id="'eye_'+indexL1"
                    :class="`fas fa-eye-slash font_light mr-2`"
                    aria-hidden="true"
                  ></i>
                  {{ keyL1 }}</span
                >
                <span>
                  <i :class="`fas fa-caret-down`" aria-hidden="true"></i>
                </span>
              </div>
              <b-collapse
                v-if="Object.keys(itemL1).length >= 1"
                :id="`summary${indexL1}L1`"
                class="mt-2"
              >
                <div
                  class="subItem1"
                  id="subItem1"
                  style="cursor: pointer"
                  v-for="(itemL2, keyL2, indexL2) in itemL1"
                  :key="`${keyL2}-${indexL2}`"
                  :class="{ active: clickedSubLink === itemL2 }"
                >
                  <div v-b-toggle="`${indexL1}${indexL2}L2`">
                    <span
                      class="sublink d-flex justify-content-between"
                      @click="renderDetails(keyL1, keyL2, itemL2)"
                    >
                      {{ keyL2 }}

                      <!-- <span v-if="keyL1 == 'MCR'">
                        <span
                        aria-hidden="true"
                        :style="{ color: itemL2.color }"
                        class="fas fa-circle float-right ml-5"
                      ></span></span>  -->

                      <span
                        >{{ itemL2.Actual }}

                        <span
                          aria-hidden="true"
                          :style="{ color: itemL2.color }"
                          class="fas fa-circle float-right ml-5"
                        ></span>
                      </span>
                    </span>
                  </div>
                </div>
              </b-collapse>
            </div>
          </transition>
        </b-list-group-item>
      </b-list-group>
      <transition name="fade">
        <div v-if="clickedDetailsVisible" class="sideBarDetailsContainer">
          <div class="clickedHeader">
            <span class="clickedTitle">{{ kmlheading }}</span>
            <span class="closeIcon">
              <i
                class="icon fa fa-times"
                aria-hidden="true"
                aria-controls="sidebar"
                @click="closeDetails()"
              ></i>
            </span>
          </div>
          <div class="clickedDetails d-flex flex-row">
            <!-- <div class="clickedItemHeader">{{ clickedDetails.sub_class }}</div> -->
            <b-list-group-item class="">
              Actual:  {{ clickedDetails.Actual }} <br> Planned: 
              {{ clickedDetails.Total }}
            </b-list-group-item>
            <apexchart
              type="radialBar"
              height="100"
              :options="options"
              :series="series"
            ></apexchart>

            <!-- <b-progress :max="100" class="p-bar">
              <b-progress-bar
                :value="total_percentage"
                :label="`${(
                  (clickedDetails.Actual / clickedDetails.Total) *
                  100
                ).toFixed()}%`"
              ></b-progress-bar>
            </b-progress> -->

            

            <!-- <b-progress :value="total_percentage" :label="`${((clickedDetails.Actual / clickedDetails.Total) * 100).toFixed()} %`" :max="100" show-value  class="p-bar"></b-progress> -->
          </div>
        </div>
      </transition>
    </div>
    <div class="text-right down_btn">
      <b-button
        pill
        class="primaryBg upload-csv"
        @click="uploadcsv()"
        v-b-tooltip.hover
        title="Upload CSV"
        ><i class="fa fa-upload"></i
      ></b-button>
      <b-dropdown
        class="download-drop"
        droptop
        variant="dark"
        html='<i class="fa fa-download"></i>'
      >
        <b-dropdown-item href="javascript:;"
        @click="downloadSummary()"
          ><i
            class="fa fa-file-text-o"
            @click="downloadSummary()"
            v-b-tooltip.hover
            title="Download CSV"
          ></i>
          Download CSV</b-dropdown-item
        >
        <b-dropdown-item href="javascript:;"
        @click="downloadSummary()"
          ><i
            class="fa fa-file-excel-o"
            @click="downloadSummary()"
            v-b-tooltip.hover
            title="Download Excel"
          ></i>
          Download Excel</b-dropdown-item
        >
      </b-dropdown>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import VueEllipseProgress from "vue-ellipse-progress";

Vue.use(VueEllipseProgress);
var clicked = true;
// var temp     
var temp1 = ""
export default {
  name: "summarySideNav",
  props: ["summaryData"],
  data() {
    
    return {
      clickedDetailsVisible: false,
      clickedDetails: {},
      clickedSummary: null,
      clickedSubLink: "",
      kmlheading: "",
      total_percentage: Number,
   
      
    };
  },
  computed: {
    
    currentDate: function () {
      let currentDate = this.$store.state.airmapStore.currentDate;
      return currentDate ? currentDate : "";
    },
    kmlBaseUrl: function () {
      let kmlBaseUrl = this.$store.state.projectStore.projectProperties.kml;
      return kmlBaseUrl ? kmlBaseUrl : "";
    },
    options: function () {
    return {
    chart: {
          height: 350,
          type: "radialBar",
        },
        plotOptions: {
          radialBar: {
            dataLabels: {
              show: true,
              name:{
                show:false
              },
              value: {
                show: true,
                offsetY: 8,
              },
            },

            hollow: {
              size: "40%",
            },
          },
        },
    }
    },
    series: function () {
      return [this.total_percentage.toFixed()]
    },
  },
  methods: {

    uploadcsv(){
      this.$bvModal.show("csvUploadModel");
    },
    lightning(i,index) {
      console.log(index)
      if (clicked) {
        document.getElementById('eye_'+index).className="far fa-eye"
        Vue.config.errorHandler = function (err, vm, info) {
          console.log(`Error: ${err.toString()}\nInfo: ${info}`);
        };

        Vue.config.warnHandler = function (msg, vm, trace) {
          console.log(`Warn: ${msg}\nTrace: ${trace}`);
        };
        var total_kml = [];

        for (var n in i) {
          console.log(i[n]);
          if (i[n].kml === undefined) {
            i[n].kml = " ";
          }
          if (i[n].color === undefined) {
            i[n].color = "rgb(26, 25, 23)";
          }

          for (var p = 0; p < i[n].kml.length; p++) {
            total_kml.push(i[n].kml[p]);
            console.log(i[n].color);

            const rgb = i[n].color
              .substring(4, i[n].color.length - 1)
              .replace(/ /g, "")
              .split(",");
            const r = parseInt(rgb[0]);
            const g = parseInt(rgb[1]);
            const b = parseInt(rgb[2]);
            let hex_color = this.rgbToHex(r, g, b);

            this.$root.$emit("kmlClicked", {
              url: `${this.kmlBaseUrl}` + "summary/" + `${i[n].kml[p]}`,
              color: String(i[n].color),
              hex: hex_color,
            });
          }
        }
        clicked = false;
      } else {
        document.getElementById('eye_'+index).className="far fa-eye-slash"
        this.$root.$emit("removeKml");
        clicked = true;
      }

      console.log(total_kml);
    },
    componentToHex(c) {
      var hex = c.toString(16);
      return hex.length == 1 ? "0" + hex : hex;
    },
    rgbToHex(r, g, b) {
      return (
        "#" +
        this.componentToHex(r) +
        this.componentToHex(g) +
        this.componentToHex(b)
      );
    },
    renderDetails(keyL2, keyL3, value) {
    
      this.clickedDetailsVisible = !this.clickedDetailsVisible;
      
      if(this.clickedDetailsVisible == false){
        if(keyL3 == temp1){
        document.getElementById('subItem1').style.fontWeight ='200';
        this.$root.$emit("removeKml");
        }
        if(keyL3 !== temp1){
        this.clickedDetailsVisible = !this.clickedDetailsVisible;
        this.clickedSubLink = value;
        this.clickedSummary = this.currentDate + keyL2 + keyL3;
        this.clickedDetails = value;
        this.kmlheading = keyL3;
        this.total_percentage =
          (this.clickedDetails.Actual / this.clickedDetails.Total) * 100;
        // console.log("dsaffafa", value);
        // console.log(value);
        const rgb = value.color
          .substring(4, value.color.length - 1)
          .replace(/ /g, "")
          .split(",");
        const r = parseInt(rgb[0]);
        const g = parseInt(rgb[1]);
        const b = parseInt(rgb[2]);
        let hex_color = this.rgbToHex(r, g, b);

        // console.log(this.kmlBaseUrl);

        //TODO change this url for solar
        if (value) {
          for (var j in value.kml) {
            this.$root.$emit("kmlClicked", {
              url: `${this.kmlBaseUrl}` + "summary/" + `${value.kml[j]}`,
              color: String(value.color),
              hex: hex_color,
            });
          }
        }
        temp1 = keyL3
        console.log(temp1)
        }
        //this.clickedDetailsVisible = false;
      }else{
         this.clickedSubLink = value;
        this.clickedSummary = this.currentDate + keyL2 + keyL3;
        this.clickedDetails = value;
        this.kmlheading = keyL3;
        this.total_percentage =
          (this.clickedDetails.Actual / this.clickedDetails.Total) * 100;
        // console.log("dsaffafa", value);
        // console.log(value);
        const rgb = value.color
          .substring(4, value.color.length - 1)
          .replace(/ /g, "")
          .split(",");
        const r = parseInt(rgb[0]);
        const g = parseInt(rgb[1]);
        const b = parseInt(rgb[2]);
        let hex_color = this.rgbToHex(r, g, b);

        // console.log(this.kmlBaseUrl);

        //TODO change this url for solar
        if (value) {
          for (var i in value.kml) {
            this.$root.$emit("kmlClicked", {
              url: `${this.kmlBaseUrl}` + "summary/" + `${value.kml[i]}`,
              color: String(value.color),
              hex: hex_color,
            });
          }
        }
        temp1 = keyL3
        console.log(temp1)
      }
        // if (clicked) {
        /* if(temp1 === keyL3){
        document.getElementById('subItem1').style.fontWeight ='200';
        this.$root.$emit("removeKml");
        this.clickedDetailsVisible = false;
        }
       else if (temp1 === " " || temp1 !== keyL3){

        this.clickedSubLink = value;
        this.clickedSummary = this.currentDate + keyL2 + keyL3;
        this.clickedDetails = value;
        this.kmlheading = keyL3;
        this.total_percentage =
          (this.clickedDetails.Actual / this.clickedDetails.Total) * 100;

        //this.clickedDetailsVisible = true;
        // console.log("dsaffafa", value);
        // console.log(value);
        const rgb = value.color
          .substring(4, value.color.length - 1)
          .replace(/ /g, "")
          .split(",");
        const r = parseInt(rgb[0]);
        const g = parseInt(rgb[1]);
        const b = parseInt(rgb[2]);
        let hex_color = this.rgbToHex(r, g, b);

        // console.log(this.kmlBaseUrl);

        //TODO change this url for solar
        if (value) {
          for (var i in value.kml) {
            this.$root.$emit("kmlClicked", {
              url: `${this.kmlBaseUrl}` + "summary/" + `${value.kml[i]}`,
              color: String(value.color),
              hex: hex_color,
            });
          }
        }
        temp1 = keyL3
        } */ 
        
    },

    
    closeDetails() {
      this.clickedDetails = {};
      this.clickedDetailsVisible = false;
      this.$root.$emit("removeKml");
    },
    downloadSummary() {
      this.$store.dispatch("airmapStore/downloadCompileApi", "downloadsummary");
    },
  },
  destroyed() {
    this.$root.$emit("removeKml");
  },
};
</script>

<style scoped>
.summaryListContainer {
  /* max-width: 100px; */
  max-height: calc(100vh - 105px);
  overflow: auto;
}
.sideBarList {
  display: flex;
  flex-direction: column;
  text-transform: capitalize;
  text-align: left;
  overflow-y: auto;
}
.sideBarList > div[data-v-dd719d44]:first-child {
  display: flex;
  justify-content: space-between;
  padding: 10px 20px;
}
.list-group-item {
  padding: 0;
}

.not-collapsed + div {
  margin: 0 !important;
}

.subItem1 {
  font-weight: 400;
  overflow: auto;
}
.subItem2 {
  padding: 10px;
  text-align: left;
}
/* .subItem2:hover {
}
.clickedHighlighter {
} */
.sideBarDetailsContainer {
  position: absolute;
  left: 370px;
  bottom: 20px !important;
  min-height: 80px;
  min-width: 250px !important;
  color: var(--dark);
  background-color: var(--light);
  box-shadow: 1px 1px var(--shadow);
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
  /* background-color: var(--primaryBg); */
  background-color: #20626dcc;

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
.clickedItemHeader {
  font-weight: bold;
  padding: 15px;
  border-bottom: 1.5px solid rgba(0, 0, 0, 0.125);
  text-transform: capitalize;
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

.font_light {
  font-weight: 100;
  font-size: 14px;
}
  /* i.fas.fa-eye {
    content: "\f070";
  } */
.not-collapsed i.fa-caret-down:before {
  content: "\f0d8";
}
.collapsed i.fa-caret-down:before {
  content: "\f0d7";
}
.sideBarList .not-collapsed {
}
.summaryListContainer {
  padding: 15px;
}

.circular {
  height: 100px;
  width: 100px;
  position: relative;
  transform: scale(2);
}
.circular .inner {
  position: absolute;
  z-index: 6;
  top: 50%;
  left: 50%;
  height: 80px;
  width: 80px;
  margin: -40px 0 0 -40px;
  background: #dde6f0;
  border-radius: 100%;
}
.circular .number {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 10;
  font-size: 18px;
  font-weight: 500;
  color: #4158d0;
}
.circular .bar {
  position: absolute;
  height: 100%;
  width: 100%;
  background: #fff;
  -webkit-border-radius: 100%;
  clip: rect(0px, 100px, 100px, 50px);
}
.circle .bar .progress {
  position: absolute;
  height: 100%;
  width: 100%;
  -webkit-border-radius: 100%;
  clip: rect(0px, 50px, 100px, 0px);
  background: #4158d0;
}
.circle .left .progress {
  z-index: 1;
  animation: left 4s linear both;
}
@keyframes left {
  100% {
    transform: rotate(180deg);
  }
}
.circle .right {
  transform: rotate(180deg);
  z-index: 3;
}
.circle .right .progress {
  animation: right 4s linear both;
  animation-delay: 4s;
}
@keyframes right {
  100% {
    transform: rotate(180deg);
  }
}
</style>
