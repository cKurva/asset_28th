<template>
  <div class="sidenav">
    <div id="sidenavLogo" role="button" @click="routeToHome">
      <h1 class="text-light m-0"><img class="logo-data-see" src="../../../../assets/img/DataSee-removebg-preview.png" ></h1>
    </div>
    <div id="dynamicLinks" class="h-70 mb-5">
      <div
        v-for="link in freemiumLinks"
        :key="link.header"
        href="#"
        @click.prevent="switchSideNav(link.header)"
        class="sidenavLinks"
        :class="{ clickedSideNav: sideBarHeader === link.header }"
        :disabled="link.isDisabled"
        :title="link.header"
        v-b-tooltip.hover.right
      >
        <i :class="link.icon" aria-hidden="true"></i>
      </div>

      <div v-if="$can('use', 'Enterprise') || $can('use', 'Premium')">
        <div
          v-for="link in premiumLinks"
          :key="link.header"
          href="#"
          @click.prevent="switchSideNav(link.header)"
          class="sidenavLinks"
          :class="{ clickedSideNav: sideBarHeader === link.header }"
          :disabled="link.isDisabled"
          :title="link.header"
          v-b-tooltip.hover.right
        >
          <i :class="link.icon" aria-hidden="true"></i>
        </div>
      </div>
    </div>
    <div id="staticLinks" class="h-30">
      <div
        class="sidenavLinks"
        @click="showProjectInfo()"
        title="Project Info."
        v-b-tooltip.hover.right
      >
        <i class="fas fa-info-circle icon-color" aria-hidden="true"></i>
      </div>
      <div
        class="sidenavLinks"
        title="Settings"
        v-b-tooltip.hover.right
      >
        <i class="fas fa-cog icon-color"></i>
      </div>
      <div
        class="sidenavLinks inner_bt_btn" @click="logout"
        title="Logout"
        v-b-tooltip.hover.right
      >

      <i class="fas fa-power-off icon-color"></i>
      </div>
    </div>
    <b-sidebar  id="sidebar" :no-header="true" :visible="sideBarVisible" shadow>
      <div class="sideBarContainer ">
        <div class="sideBarMenuContainer">
          <div class="headingContainer">
            <b class="title text-dark">{{ sideBarHeader }}</b>
            <!-- <span class="icon-container">
              <i
                class="icon fa fa-times"
                aria-hidden="true"
                aria-controls="sidebar"
                @click="closeSideBar('assetMenu')"
              ></i>
            </span> -->
          </div>
          <div>
            <summarySideNav
              v-if="sideBarHeader === 'Summary'"
              :summaryData="summaryApiData"
            />
            <assetsSideNav
              v-if="sideBarHeader === 'Assets'"
              :assetData="summaryAssetData"
            />
            <deviation
              v-if="sideBarHeader === 'Deviation'"
              :deviationdata="deviationApiData"
            />
            <inverterSideNav
              v-if="sideBarHeader === 'Inverter'"
              :inverterApiData="inverterApiData"
            />
            <inverterdeviation
            v-if="sideBarHeader === 'InverterDeviation'"
            :inverterdeviationApiData="inverterdeviationApiData"
            />

            <aoiSideNav v-if="sideBarHeader === 'AOI'" :aoiArray="AOIData" />
            <measureSideNav
              v-if="sideBarHeader === 'Measure'"
              :measuresArray="measureData"
            />
            <cadAlignSideNav
              v-if="sideBarHeader === 'CAD'"
              :cadArray="cadAlignData"
            />
            <activitySideNav
              v-if="sideBarHeader === 'Activities'"
              :activityArray="activityData"
            />
          </div>
        </div>
      </div>
    </b-sidebar>
    <div class="projectInfoContainer">
      <b-alert v-model="isProjectInfoShown" variant="light" dismissible fade class="projectinfo-alert">
        <h4 class="alert-heading">{{ projectInfo.projectName }}</h4>
        <p>
          {{ projectInfo.projectType }}
        </p>
        <hr />
      </b-alert>
    </div>
    
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import summarySideNav from "./summarySideNav";
import deviation from "./deviation";
import inverterdeviation from  "./inverterdeviation";
import assetsSideNav from "./assetsSideNav";
import inverterSideNav from "./inverterSideNav";
import activitySideNav from "./activitySideNav";
import aoiSideNav from "./aoiSideNav";
import measureSideNav from "./measureSideNav";
import cadAlignSideNav from "./cadAlignSideNav";

export default {
  name: "sidenav",
  components: {
    summarySideNav,
    assetsSideNav,
    inverterSideNav,
    aoiSideNav,
    measureSideNav,
    cadAlignSideNav,
    activitySideNav,
    deviation,
    inverterdeviation,
  },
  data() {
    return {
      sidebarId: "",
      sideBarHeader: "",
      sideBarVisible: false,
      clickedSideNav: "",
      sideBarContent: {},
      isProjectInfoShown: false,
    };
  },
  computed: {
    ...mapGetters({
      summaryApiData: "projectStore/getSummaryData",
      summaryAssetData: "projectStore/getSummaryAssetData",
      inverterApiData: "projectStore/getInverterData",
      inverterdeviationApiData: "projectStore/getInverterDivData",
      deviationApiData: "projectStore/getDeviationData",
      activityData: "projectStore/getActivityData",
      AOIData: "airmapStore/getAllAOIs",
      measureData: "airmapStore/getAllMeasures",
      cadAlignData: "airmapStore/getAllCadAlign",
      projectInfo: "projectStore/getProjectInfo",
    }),
    freemiumLinks: function () {
      let sideBarLinks = [];
      let projectType = this.$store.state.projectStore.projectType;
      if (
        this.$store.state.projectStore.currentDateProject.type ===
          "SCQM+SCPM" ||
        this.$store.state.projectStore.currentDateProject.type === "SCQM"
      ) {
        if (projectType === "Construction_Site") {
          sideBarLinks = [
            { header: "Assets", icon: "fas fa-chart-pie icon-color" },
            { header: "Compare", icon: "fa fa-arrows-h icon-color" },
          ];
        } else {
          sideBarLinks = [
            // { header: "Summary", icon: "fas fa-chart-pie" },
            // { header: "Inverter", icon: "fas fa-adjust" },
            { header: "InverterDeviation", icon: "fa fa-random icon-color"},
            { header: "Deviation", icon: "fa fa-map-signs icon-color" },
            { header: "Compare", icon: "fa fa-arrows-h icon-color" },
          ];
        }
      }

       else {
        if (projectType === "Construction_Site") {
          sideBarLinks = [
            { header: "Assets", icon: "fas fa-chart-pie icon-color" },
            { header: "Compare", icon: "fa fa-arrows-h icon-color" },
          ];
        } else {
          sideBarLinks = [
            { header: "Summary", icon: "fas fa-chart-pie icon-color" },
            { header: "Inverter", icon: "fas fa-adjust icon-color" },
            { header: "Compare", icon: "fa fa-arrows-h icon-color" },
          ];
        }
      }

      return sideBarLinks;
    },
    premiumLinks: function () {
      let sideBarLinks = [];
      sideBarLinks.push(
        { header: "3D model", icon: "fa fa-cube icon-color" },
        { header: "AOI", icon: "fa fa-object-group icon-color" },
        { header: "Measure", icon: "fa fa-ruler-combined icon-color" },
        { header: "CAD", icon: "fa fa-cloud-upload icon-color" }
      );
      if (this.$store.state.userStore.isEnterpriseAdmin) {
        sideBarLinks.push({ header: "Activities", icon: "fas fa-tasks icon-color" });
      }
      return sideBarLinks;
    },
  },
  methods: {
    showProjectInfo() {
      this.closeSideBar();
      this.isProjectInfoShown = !this.isProjectInfoShown;
    },
    switchSideNav(header) {
      this.isProjectInfoShown = false;
      if (this.sideBarHeader.trim() === header.trim()) {
        this.closeSideBar();
      } else {
        switch (header) {
          case "Summary":
          case "Assets":
          case "Deviation":
          case "InverterDeviation":
          case "Inverter":
          case "Activities":
            this.sideBarVisible = true;
            break;
          case "Compare":
            {

              this.sideBarVisible = false;
              let projectId = this.$route.params.projectId;
              this.$router.push(`/projects/compare/${projectId}`);
            }
            break;
            case "3D model":{
               this.sideBarVisible = false;
              let projectId = this.$route.params.projectId;
              this.$router.push(`/projects/3dModel/${projectId}`);
          }
          break;
          //Not using waterfall
          case "AOI":
            {
              this.sideBarVisible = true;
            }
            break;
          case "Measure":
            {
              this.sideBarVisible = true;
            }
            break;
          case "CAD":
            {
              this.sideBarVisible = true;
            }
            break;
          default:
            break;
        }
        this.sideBarHeader = header;
      }
    },
    closeSideBar() {
      this.sideBarVisible = false;
      this.sideBarHeader = "";
    },
    routeToHome() {
      this.$router.push("/home");
    },
    logout() {
      this.$store.dispatch("userStore/logout");
      this.$router.push("/login");
    },
  },
};
</script>
<style>
.b-sidebar-outer .shadow{
    box-shadow: 20px -23px 20px -8px #312d2d !important;
    margin-left: 2px;
}
.inner_bt_btn .btn-dark{
border-radius:0 !important;
}

</style>
<style scoped>
.sidenav {
  min-width: 3.5rem;
  height: 100%;
  position: fixed;
  left: 0;
  z-index: 999;
  background-color: var(--primaryBgOne);
  /* background-color:#00acee; */
  /* background-color : #192e5b; */
  background-color :#214950;
  background-color: rgb(240, 241, 245);
  box-shadow: 2px 2px var(--shadow);
  color: white;
}
.logo-data-see{
  width: 36px;
}
div.sidenavLinks {
  color: var(--light);
  height: 2rem;
  width: 55px;
  
  cursor: pointer;
}
div.sidenavLinks:hover {
  color: #fff;
}
.sideBarContainer {
  display: flex;
  flex-direction: column;
  height: 100%;
 
}
.sideBarMenuContainer {
  flex: 2;
  overflow-y: auto;
}
.headingContainer {
  display: flex;
  justify-content: space-between;
  height: 45px;
  padding: 12px 14px;
  color: var(--light);
  background-color: #20626dcc;
  background-color:rgb(240, 241, 245);
  box-shadow: 1px 1px var(--shadow);
  border-top-left-radius: 0px;
  border-top-right-radius: 0px;
  padding-left:30px;
}
.headingContainer .icon-container {
  transition: 0.3s;
}
.icon-color{
  color: rgb(3, 34, 92);
}
.clickedSideNav {
  color: var(--primaryBg) !important;
  background-color: var(--light);
}
.projectInfoContainer {
  position: absolute;
  width: 15vw;
  min-width: 20px;
  bottom: 10%;
  left: 100%;
}
.btn{border-radius:0px !important}



</style>
