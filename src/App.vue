<template>
    <VideoTable />
</template>

<script>
import VideoTable from "./components/VideoTable.vue";

export default {
    name: "App",
    data() {
        return {
            playVideo: VideoTable.data().playVideo,
            playVideoName: VideoTable.data().playVideoName,
        };
    },
    components: {
        VideoTable,
    },
    // methods: {},
    // mounted() {},
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
}
</style>
