<template>
  <div id="app">
    <TopBar ref="topBar" />
    <div class="main-content" :style="mainContentStyle">
        <appNavbar @option-selected="changeTab"/>
        <MainArea :traffic="showTrafficValue" :showCameras="showCamerasValue" :activeTab="activeTab" @emitChosenCoords="setChosenCoords"/>
        <RightPanel :coords="currentChosenCoords" :activeTab="activeTab" @toggle-traffic="toggleTraffic" @toggle-cctv="toggleCCTV"/>
    </div>
  </div>
</template>

<script>
import TopBar from '../components/TopBar'
import appNavbar from '../components/appNavbar'
import MainArea from '../components/MainArea'
import RightPanel from '../components/RightPanel'

export default {
  name: 'App',
  components: {
    TopBar,
    appNavbar,
    MainArea,
    RightPanel
  },
  data() {
    return {
      activeTab: 'traffic', // Default tab
      headerHeight: 0, // Initialize header height
      showTrafficValue: false,
      showCamerasValue: false,
      currentChosenCoords: "",
    }
  },
  computed: {
    mainContentStyle() {
      const height = `calc(100vh - ${this.headerHeight}px)`;
      return { height };
    }
  },
  mounted() {
    // Use the $nextTick method to ensure the DOM is updated
    this.$nextTick(() => {
      // Get the height of the header
      this.headerHeight = this.$refs.topBar.$el.offsetHeight;
    });
  },
  methods: {
    changeTab(tab) {
      this.activeTab = tab;
    },
    toggleTraffic(newVal) {
      this.showTrafficValue = newVal;
    },
    toggleCCTV(newVal) {
      this.showCamerasValue = newVal;
    },
    setChosenCoords(val) {
      this.currentChosenCoords = val;
    }
  }
}
</script>

<style>
.main-content {
  display: flex;
}
</style>
