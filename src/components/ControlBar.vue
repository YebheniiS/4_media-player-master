<template>
    
    <div :class="['control-bar  px-4 w-full', (! hidden && nextNodeId === 0) ? 'showing' : 'not-showing']"  @mouseleave="hideControlBar"  @mouseover="mouseOver">
        <div
            class="ctls left-ctls rounded-lg justify-center"
            :style="{
                background,
                color,
            }"
        >
            <button class="control-btn ctl-item">
                <i
                    v-if="!playing"
                    class="fa-solid fa-play text-xl"
                    @click="setPlaying(true)"
                ></i>
                <i
                    v-else
                    class="fa-solid fa-pause text-xl"
                    @click="setPlaying(false)"
                ></i>
            </button>
        </div>

        <div
            class="ctls center-ctls rounded-lg mx-4 relative flex items-start"
            :style="{
                background,
                color,
            }"
        >
            <Timeline :background="background" :color="color" />
        </div>

        <div
            class="ctls right-ctls rounded-lg px-3 space-x-4"
            :style="{
                background,
                color,
            }"
        >
            <button
                :class="['control-btn  ', 'ctl-item', '']"
            >
                <VolumeControl
                    :background="background"
                    :color="color"
                />
            </button>

            <!-- <button :class="['control-btn', 'ctl-item']">
                <i class="fa-solid fa-gear fa-xl"></i>
            </button> -->

            <button v-if="false" :class="['control-btn', 'ctl-item']">
                <i class="fa-solid fa-closed-captioning fa-xl"></i>
                <!-- <i class="fa-solid fa-closed-captioning-slash"></i> -->
            </button>

            <button 
                v-if="project.allow_share"
                class="control-btn icon setting-btn" 
                @click="toggleShare"
            >
                <i class="fa-solid fa-share-nodes fa-xl"></i>
            </button>

            <button
                v-if="project.chapters"
                class="control-btn icon setting-btn"
                @click="handleChaptersClicked"
            >
                <i class="fa-solid fa-list fa-xl"></i>
            </button>

            <button
                class="control-btn icon setting-btn"
                @click="toggleFullScreen"
            >
                <i v-if="!inFullScreen" class="fa-solid fa-expand fa-xl"></i>
                <i v-else class="fa-solid fa-compress fa-xl"></i>
            </button>
        </div>
    </div>
</template>

<script setup>
import useProject from '../hooks/useProject';
import { ref, computed } from 'vue';
import Timeline from './Timeline.vue';
import usePlayer from '../hooks/usePlayer';
import VolumeControl from './VolumeControl.vue';
import Volume from './Volume.vue';
import useControlBar from '../hooks/useControlBar'
import useNode from '../hooks/useNode'
import { notifyParent } from "../lib/wrapperUtils";
import constants from "../constants";

const { project, fetched } = useProject();
const { setPlaying, playing, inFullScreen } = usePlayer();
const { hidden, toggleChapters, toggleShare } = useControlBar();
const { nextNodeId } = useNode();

const background = computed(() => {
    if (!fetched.value) return '';
    return project.value.player_skin.controls.background;
});

const color = computed(() => {
    if (!fetched.value) return '';
    return project.value.player_skin.controls.color;
});


const { showControlBar, hideControlBar} = useControlBar();

const mouseOver = e => {
    showControlBar()    
}

const toggleFullScreen = () => {    
    notifyParent(constants.PLAYER_EVENTS.TOGGLE_FS)
}

const handleChaptersClicked = () => {
    setPlaying(false);
    toggleChapters();
}


</script>

<style scoped lang="scss">
.control-bar {
    &.showing {
        opacity: 1;
        transform: translateY(0);
        // z-index: 4000;
    }
    &.not-showing {
        transform: translateY(10%);
        opacity: 0;
    }
    position: absolute;
    bottom: 0em;
    padding-bottom: 1em;
    height: 5em;
    z-index: 4000;
    display: flex;
    justify-content: space-between;
    align-items: center;

    transition: transform 0.2s ease-in, all 0.3s ease-out;
}
.ctls {
    height: 100%;
    display: flex;
}
.center-ctls {
    flex: 1;
    height: 3.2em;
}
.right-ctls {
    height: 3.2em;
    align-items: center;
}
// left ctl is play button
.left-ctls {
    width: 4em;
    height: 4em;
    align-items: center;
}
.ctl-duration {
    height: 30px;
    display: flex;
    align-items: center;
    font-size: 1.1em;
    padding-left: 6px;
    color: inherit;
    line-height: 1;
}
</style>
