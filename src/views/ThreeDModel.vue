<template>
<div class="btn-back">
<!-- <b-button
        @click="returnToMap"
        variant="dark"
        title="Back to Map"
        class="btn-back"
        v-b-tooltip.hover.right
      >
        <i class="fas fa-arrow-left"></i>
      </b-button> -->
    <div class="main-container">
        <iframe class="main-container" :src="url">
       </iframe>
    </div>
    </div>
</template>
<script>
export default {
    name: "3dModel",
    data(){
        return{
            url : "https://takvaviya.in/files/3d_model/airmap_020620/3d.html"
        }
    },
    methods:{
         returnToMap() {
      this.$router.go(-1);
    },
    }
}
</script>
<style scoped>
.main-container{
  height: 100vh;
  width: 100%;
  display:flex;
  flex-direction: row;
}
.button-back{
    width: 200px;
    background-color: grey;
}
.btn-back{
    background-color: #292b2c;
}
</style>