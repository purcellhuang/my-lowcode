<template>
    <div class="infinite-list">
        <div class="infinite-list__wrap" id="scrollBox" ref="scrollBoxRef">
            <div class="infinite-list__box" >
                <div class="infiiete-list__box-top" ref="scrollBoxTop">
                    <slot></slot>
                </div>
                <div class="infinite-list__box-bottom">
                    <slot></slot>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { onMounted, reactive, ref } from "vue";
import gsap from "gsap"

const props = defineProps({

})


const scrollBoxTop = ref<HTMLElement | undefined>()
const scrollBoxRef = ref<HTMLElement | undefined>()

let scrollIndex = 0
const scrollEvent = () => {
    const childrenNodes = Array.from(scrollBoxTop.value?.childNodes || []).filter(el => el.nodeType === Node.ELEMENT_NODE)
    const currentScrollEl: HTMLElement = childrenNodes[scrollIndex] as HTMLElement
    const rect = currentScrollEl.getBoundingClientRect()
    scrollIndex = (scrollIndex + 1) % childrenNodes.length; // 取完记得让 `scrollingElIndex` 下标+1，但只能在元素个数之内循环

    let elHeight = rect.height
    let targetScrollTop = currentScrollEl.offsetTop + elHeight

    gsap.to('#scrollBox', {scrollTop: targetScrollTop, duration: 1, onComplete: () => {
        if(scrollIndex === 0) {
        targetScrollTop = 0
        gsap.to('#scrollBox', {scrollTop: targetScrollTop, duration: 0, onComplete: () => {
            scrollEvent()

        }})
        return
    }
            scrollEvent()
        }})


}
onMounted(() => {
    scrollEvent()
})
</script>

<style scoped lang="scss">

.infinite-list {
    position: relative;
    height: 400px;
    border: 1px solid black;
    .infinite-list__wrap {
        height: 100%;
        overflow-y: auto;
    }
}
</style>