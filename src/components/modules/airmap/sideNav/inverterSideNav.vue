<template>
  <div>
    <transition name="slide-fade" mode="out-in">
      <div :key="currentDate">
        <div class="inverterListContainer customScroll">
          <!-- <b-form-select
          
            class="project-select"
            :options="['Name 01','Name 02']"
            
            >
          </b-form-select> -->
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
          <!-- <transition name="slide-fade" mode="out-in">
            <div :key="currentDate">
              <div
                class="d-flex justify-content-between sidenavListItem"
                :class="{
                  clickedHighlighter:
                    clickedAsset === currentDate + key + index,
                }"
                @click="renderDetails(key, index, asset)"
                v-for="(asset, key, index) in inverterApiData.invertersData[
                  inverterCurrentPage
                ]"
                :key="index"
              >
                <div>
                  <div v-b-toggle="`dev${index}`" style="color: black">                    
                {{ key.toLowerCase() | capitalize }}
                  </div>
                </div>
                <div>
                  <i
                    aria-hidden="true"
                    :style="{ color: `rgb(${String(asset.color)})` }"
                    class="fas fa-circle float-right"
                  ></i>
                </div>
                <b-collapse
                  v-if="Object.values(asset).length >= 1"
                  :id="`dev${index}`"
                  class="mt-4"
                  style="margin-right: 30px"
                >
                  <div style="color: black">
                    <span>Actual : {{ asset.actual }}</span
                    ><br />
                    <span>Total : {{ asset.total }}</span>
                  </div>
                </b-collapse>
              </div>
            </div>
          </transition> -->
          <b-list-group>
            <b-list-group-item
              v-for="(itemL1, keyL1, indexL1) in inverterApiData.test[
                this.inverterCurrentPage
              ]"
              :key="keyL1"
            >
              <transition name="slide-fade" mode="out-in">
                <div class="sideBarList" :key="currentInverter">
                  <div v-b-toggle="`${currentInverter}${indexL1}L1`">
                    <span
                      ><i
                        @click="lightning(itemL1)"
                        :class="`fas fa-eye font_light mr-2`"
                        aria-hidden="true"
                      ></i
                      >{{ keyL1 }}</span
                    >
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
                      style="cursor: pointer"
                      v-for="(itemL2, keyL2, indexL2) in itemL1"
                      :key="`${keyL2}-${indexL2}`"
                      :class="{ active: clickedSubLink === itemL2 }"
                    >
                      <div
                        v-b-toggle="`${currentInverter}${indexL1}${indexL2}L2`"
                      >
                        <span
                          class="d-flex justify-content-between"
                          @click="renderDetails(keyL1, keyL2, itemL2)"
                        >
                          {{ keyL2 }}

                          <span
                            >{{ itemL2.Actual }}
                            <i
                              aria-hidden="true"
                              :style="{ color: itemL2.color }"
                              class="fas fa-circle float-right ml-5"
                            ></i>
                          </span>
                        </span>
                      </div>
                    </div>
                  </b-collapse>
                </div>
              </transition>
            </b-list-group-item>
          </b-list-group>
        </div>

        <!-- <transition name="slide-fade" mode="out-in">
          <h4 class="text-center" :key="currentInverter">
            {{ currentInverter }}
          </h4>
        </transition> -->

        <!-- <div class="inverterPaginator">
          <b-pagination
            :total-rows="inverterTotalPages"
            v-model="inverterCurrentPage"
            :per-page="1"
            align="center"
            @change="inverterPageChange"
            size="sm"
          ></b-pagination>
        </div> -->

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
            <div class="clickedDetails">
              <b-list-group-item class="text-center">
                Actual : <b>{{ this.clickedDetails.Actual }}</b> | Planned :
                <b>{{ this.clickedDetails.Total }}</b>
              </b-list-group-item>
              <!-- <b-progress :value="12764" :max="15274" show-value class="p-bar"></b-progress>
               -->

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
                    (this.clickedDetails.Actual / this.clickedDetails.Total) *
                    100
                  ).toFixed()}%`"
                ></b-progress-bar>
              </b-progress> -->
            </div>
          </div>
        </transition>
      </div>
    </transition>
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
        @click="downloadInverter()"
          ><i
            class="fa fa-file-text-o"
            
            v-b-tooltip.hover
            title="Download CSV"
          ></i>
          Download CSV</b-dropdown-item
        >
        <b-dropdown-item href="javascript:;"
        @click="downloadInverter()"
          ><i
            class="fa fa-file-excel-o"
            
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
var clicked = true;
var temp1 = "";
export default {
  name: "inverterSideNav",
  props: ["inverterApiData"],
  data() {
    return {
      clickedDetailsVisible: false,
      clickedDetails: {},
      clickedInverter: null,
      actual: {},
      total: {},
      clickedSubLink: "",
      inverterCurrentPage: this.inverterApiData.inverters[0],
      total_percentage: Number,
      kmlheading: "",
      select: {
        selected: this.inverterApiData.inverters[0],
        options: this.inverterApiData.inverters,
      },
    };
  },
  computed: {
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
              name: {
                show: false,
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
      };
    },
    series: function () {
      return [this.total_percentage.toFixed()];
    },
    currentDate: function () {
      let currentDate = this.$store.state.airmapStore.currentDate;
      return currentDate;
    },
    inverterTotalPages: function () {
      let inverterPages = this.inverterApiData.inverters.length;

      return inverterPages ? inverterPages : 0;
    },
    kmlBaseUrl: function () {
      let kmlBaseUrl = this.$store.state.projectStore.projectProperties.kml;
      return kmlBaseUrl ? kmlBaseUrl : "";
    },
    currentInverter: function () {
      let currentInverter = this.inverterCurrentPage;

      this.$root.$emit("kmlClicked", {
        url:
          `${this.kmlBaseUrl}` + "inverter/" + `${currentInverter}` + "/gb.kml",
        kml_type: "gb",
      });

      return currentInverter ? currentInverter : {};
    },
  },

  methods: {
    uploadcsv() {
      this.$bvModal.show("csvInvUploadModel");
    },
    lightning(i) {
      if (clicked) {
        Vue.config.errorHandler = function (err, vm, info) {
          console.log(`Error: ${err.toString()}\nInfo: ${info}`);
        };

        Vue.config.warnHandler = function (msg, vm, trace) {
          console.log(`Warn: ${msg}\nTrace: ${trace}`);
        };

        var total_kml = [];
        var color_kml_list = [];
        var reverse_color = [];
        var reverse_kml = [];

        for (var n in i) {
          if (i[n].kml === undefined) {
            i[n].kml = " ";
          }
          if (i[n].color === undefined) {
            i[n].color = "rgb(26, 25, 23)";
          }

          console.log(i[n].kml);
          total_kml.push(i[n].kml);
          // color_kml_list.push(i[n].color)
          var rgb = i[n].color
            .substring(4, i[n].color.length - 1)
            .replace(/ /g, "")
            .split(",");
          const r = parseInt(rgb[0]);
          const g = parseInt(rgb[1]);
          const b = parseInt(rgb[2]);
          let hex_color = this.rgbToHex(r, g, b);
          color_kml_list.push(hex_color);
        }

        reverse_kml = total_kml.reverse();
        reverse_color = color_kml_list.reverse();
        this.$root.$emit("kmlClicked", {
          url:
            `${this.kmlBaseUrl}` +
            "inverter/" +
            `${this.currentInverter}` +
            "/gb.kml",
          kml_type: "gb",
        });

        for (var a = 0; a < reverse_kml.length; a++) {
          this.$root.$emit("kmlClicked", {
            url:
              `${this.kmlBaseUrl}` +
              "inverter/" +
              `${this.currentInverter}/` +
              `${reverse_kml[a]}`,
            color: String(i[n].color),
            hex: reverse_color[a],
          });
        }
        clicked = false;
      } else {
        this.$root.$emit("kmlClicked", {
          url:
            `${this.kmlBaseUrl}` +
            "inverter/" +
            `${this.currentInverter}` +
            "/gb.kml",
          kml_type: "gb",
        });

        clicked = true;
      }
      console.log(clicked);
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
      // if (clicked) {
        this.clickedDetailsVisible = !this.clickedDetailsVisible;
         if(this.clickedDetailsVisible == false){
          if(keyL3 == temp1){
       this.$root.$emit("kmlClicked", {
          url:
            `${this.kmlBaseUrl}` +
            "inverter/" +
            `${this.currentInverter}` +
            "/gb.kml",
          kml_type: "gb",
        });
        }
         if(keyL3 !== temp1){
         this.clickedInverter = keyL2 + keyL3;
        this.clickedDetails = value;
        this.kmlheading = keyL3;
        this.clickedSubLink = value;
        this.total_percentage =
          (this.clickedDetails.Actual / this.clickedDetails.Total) * 100;
        const rgb = value.color
          .substring(4, value.color.length - 1)
          .replace(/ /g, "")
          .split(",");
        const r = parseInt(rgb[0]);
        const g = parseInt(rgb[1]);
        const b = parseInt(rgb[2]);
        let hex_color = this.rgbToHex(r, g, b);
        console.log(value.kml);

        if (value.actual || value.total) {
          console.log("a");
          this.actual = value.actual;
          this.total = value.total;
        } else if (value.actual === 0 || value.total === 0) {
          this.actual = value.actual;
          this.total = value.total;
        } else {
          this.actual = value.Actual_value;
          this.total = value.Current_value;
        }

        this.clickedDetailsVisible = true;
        // this.$store.dispatch("airmapStore/getKmlAPI");
        if (value) {
          if (value.kml) {
            // console.log(String(value.color))
            this.$root.$emit("kmlClicked", {
              url:
                `${this.kmlBaseUrl}` +
                "inverter/" +
                `${this.currentInverter}` +
                "/gb.kml",
              kml_type: "gb",
            });
            this.$root.$emit("kmlClicked", {
              url:
                `${this.kmlBaseUrl}` +
                "inverter/" +
                `${this.currentInverter}/` +
                `${value.kml}`,
              color: String(value.color),
              hex: hex_color,
            });
          }
        }
        temp1 = keyL3
        }
    }else{
      this.clickedInverter = keyL2 + keyL3;
        this.clickedDetails = value;
        this.kmlheading = keyL3;
        this.clickedSubLink = value;
        this.total_percentage =
          (this.clickedDetails.Actual / this.clickedDetails.Total) * 100;
        const rgb = value.color
          .substring(4, value.color.length - 1)
          .replace(/ /g, "")
          .split(",");
        const r = parseInt(rgb[0]);
        const g = parseInt(rgb[1]);
        const b = parseInt(rgb[2]);
        let hex_color = this.rgbToHex(r, g, b);
        console.log(value.kml);

        if (value.actual || value.total) {
          console.log("a");
          this.actual = value.actual;
          this.total = value.total;
        } else if (value.actual === 0 || value.total === 0) {
          this.actual = value.actual;
          this.total = value.total;
        } else {
          this.actual = value.Actual_value;
          this.total = value.Current_value;
        }

        this.clickedDetailsVisible = true;
        // this.$store.dispatch("airmapStore/getKmlAPI");
        if (value) {
          if (value.kml) {
            // console.log(String(value.color))
            this.$root.$emit("kmlClicked", {
              url:
                `${this.kmlBaseUrl}` +
                "inverter/" +
                `${this.currentInverter}` +
                "/gb.kml",
              kml_type: "gb",
            });
            this.$root.$emit("kmlClicked", {
              url:
                `${this.kmlBaseUrl}` +
                "inverter/" +
                `${this.currentInverter}/` +
                `${value.kml}`,
              color: String(value.color),
              hex: hex_color,
            });
          }
        }
        temp1 = keyL3
    }
        /* if(temp1 === keyL3){
          this.$root.$emit("kmlClicked", {
          url:
            `${this.kmlBaseUrl}` +
            "inverter/" +
            `${this.currentInverter}` +
            "/gb.kml",
          kml_type: "gb",
        });
        this.clickedDetailsVisible = false;
        }
        else if (temp1 === " " || temp1 !== keyL3){


        this.clickedInverter = keyL2 + keyL3;
        this.clickedDetails = value;
        this.kmlheading = keyL3;
        this.clickedSubLink = value;
        this.total_percentage =
          (this.clickedDetails.Actual / this.clickedDetails.Total) * 100;
        const rgb = value.color
          .substring(4, value.color.length - 1)
          .replace(/ /g, "")
          .split(",");
        const r = parseInt(rgb[0]);
        const g = parseInt(rgb[1]);
        const b = parseInt(rgb[2]);
        let hex_color = this.rgbToHex(r, g, b);
        console.log(value.kml);

        if (value.actual || value.total) {
          console.log("a");
          this.actual = value.actual;
          this.total = value.total;
        } else if (value.actual === 0 || value.total === 0) {
          this.actual = value.actual;
          this.total = value.total;
        } else {
          this.actual = value.Actual_value;
          this.total = value.Current_value;
        }

        this.clickedDetailsVisible = true;
        // this.$store.dispatch("airmapStore/getKmlAPI");
        if (value) {
          if (value.kml) {
            // console.log(String(value.color))
            this.$root.$emit("kmlClicked", {
              url:
                `${this.kmlBaseUrl}` +
                "inverter/" +
                `${this.currentInverter}` +
                "/gb.kml",
              kml_type: "gb",
            });
            this.$root.$emit("kmlClicked", {
              url:
                `${this.kmlBaseUrl}` +
                "inverter/" +
                `${this.currentInverter}/` +
                `${value.kml}`,
              color: String(value.color),
              hex: hex_color,
            });
          }
        }
        temp1 = keyL3 */
        
    },
    closeDetails() {
      this.clickedDetails = {};
      this.clickedDetailsVisible = false;
      this.$root.$emit("removeKml");
    },
    inverterPageChange(page) {
      console.log(page);
      // this.currentInverter.currentInverter = page
      // this.clickedInverter = "";
      this.inverterCurrentPage = page;
    },
    downloadInverter() {
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

<style scoped>
.inverterListContainer {
  max-height: 65vh;
  overflow: auto;
}
.sideBarList {
  display: flex;
  flex-direction: column;
  text-transform: capitalize;
  text-align: left;
  overflow-y: auto;
}
.subItem1 {
  padding: 10px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.125);
  overflow: auto;
}
.subItem2 {
  padding: 10px;
  text-align: center;
}
.subItem2:hover {
  border: 1px solid rgba(0, 0, 0, 0.125);
}
.clickedHighlighter {
  background-color: var(--primaryBg);
  color: var(--light);
}
.inverterPaginator {
  margin-top: 20px;
}
.sideBarDetailsContainer {
  position: absolute;
  left: 350px;
  bottom: 30px !important;
  min-width: 250px !important;
  min-height: 180px;
  color: var(--dark);
  background-color: var(--light);
  box-shadow: 1px 1px var(--shadow);
  border-radius: 10px;
  transition: all 1s ease-in;
}
.sideBarDetailsContainer div:last-child {
  border-radius: 0 0 10px 10px;
}
.clickedHeader {
  display: flex;
  justify-content: space-between;
  background-color: var(--primaryBg);
  color: var(--light);
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
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
.inverterListContainer {
  padding: 15px;
}

.collapsed i.fas.fa-eye::before {
  content: "\f070";
}
.not-collapsed i.fa-caret-down:before {
  content: "\f0d8";
}
.collapsed i.fa-caret-down:before {
  content: "\f0d7";
}
</style>
