<template>
<div class="dock-container">
    <ul class="dock-list">
        <li v-for="(app, index) in apps" :key="index">
            <div @click="openApp()" class="app-icon" :style="'background-image(' + app[index].icon + ')'"></div>
        </li>
    </ul>
</div>
</template>

<script>
export default {
    data() {
        return {
            apps: [
                {
                    name: "Github",
                    src:"https://github.com",
                    icon: "../assets/apps/github.png"
                },
                {
                    name: "Figma",
                    src:"https://figma.com",
                    icon: "../assets/apps/figma.svg"
                },
                {
                    name: "GMail",
                    src:"https://docs.google.com",
                    icon: ""
                }
            ]
        }
    },
    methods: {
        openApp() {
            console.log('opening app...');
        }
    }
}
</script>

<style lang="scss" scoped>
.app-icon {
    background: red;
    height: 80px;
    width: 80px;
    border-radius: 100%;
}

.dock-list {
    background: yellow;
    display: flex;
}

.dock-container {
    position: absolute;
    background: black;
    width: max-content;
    height: 120px;
    z-index: 999999;
    bottom: 0px;
    
}
</style>