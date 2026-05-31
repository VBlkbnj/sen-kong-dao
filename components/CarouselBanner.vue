<template>
  <div class="relative w-full overflow-hidden pt-1 pb-2" ref="containerRef">
    <!-- 轮播轨道 -->
    <div
      class="flex"
      :style="trackStyle"
      @touchstart="onTouchStart"
      @touchmove="onTouchMove"
      @touchend="onTouchEnd"
      @mousedown="onMouseDown"
      @transitionend="onTransitionEnd"
    >
      <div
        v-for="(s, i) in extendedSlides"
        :key="i"
        class="flex-shrink-0 flex items-center justify-center"
        :style="{ width: slideWidth + 'px', height: cardHeight + 'px', padding: '0 6px' }"
      >
        <div class="w-full h-full rounded-[8px] bg-gray-300 flex items-center justify-center">
          <span class="text-gray-400 text-sm">Slide {{ s + 1 }}</span>
        </div>
      </div>
    </div>

    <!-- 底部指示器（覆盖在卡片上方） -->
    <div class="absolute bottom-[15px] left-0 right-0 z-10 flex items-center justify-center gap-2">
      <button
        v-for="(_, i) in slides"
        :key="i"
        class="rounded-full transition-all duration-300"
        :class="i === realCurrent
          ? 'w-4 h-2 bg-gray-900'
          : 'w-2 h-2 bg-gray-400'"
        @click="goTo(i)"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'

const slides = [0, 1, 2]
// 首尾加克隆: [2, 0, 1, 2, 0]
const extendedSlides = [slides[slides.length - 1], ...slides, slides[0]]
const LAST = extendedSlides.length - 1

// virtualIndex → 真实 slide 索引（用于指示器）
function realIndex(vIdx: number): number {
  if (vIdx === 0) return slides.length - 1
  if (vIdx === LAST) return 0
  return vIdx - 1
}

const virtualIndex = ref(1)
const realCurrent = computed(() => realIndex(virtualIndex.value))

const containerRef = ref<HTMLElement | null>(null)
const containerWidth = ref(375)
const slideWidth = computed(() => containerWidth.value)
const cardHeight = 184

// ---- 拖拽 ----
const dragging = ref(false)
const jumping = ref(false)
const sliding = ref(false)   // transition 进行中，阻止自动播放越界触发
const dragOffset = ref(0)
let startX = 0

const trackStyle = computed(() => {
  const w = slideWidth.value
  const base = -virtualIndex.value * w
  return {
    transform: `translateX(${base + dragOffset.value}px)`,
    transition: (dragging.value || jumping.value) ? 'none' : 'transform 0.3s ease-out',
  }
})

// ---- 触摸 ----
function onTouchStart(e: TouchEvent) {
  dragging.value = true
  startX = e.touches[0].clientX
  dragOffset.value = 0
  stopAutoPlay()
}

function onTouchMove(e: TouchEvent) {
  if (!dragging.value) return
  dragOffset.value = e.touches[0].clientX - startX
}

function onTouchEnd() {
  if (!dragging.value) return
  dragging.value = false
  finishDrag()
}

// ---- 鼠标 ----
function onMouseDown(e: MouseEvent) {
  dragging.value = true
  startX = e.clientX
  dragOffset.value = 0
  stopAutoPlay()

  const onMove = (ev: MouseEvent) => {
    if (!dragging.value) return
    dragOffset.value = ev.clientX - startX
  }
  const onUp = () => {
    if (!dragging.value) return
    dragging.value = false
    finishDrag()
    document.removeEventListener('mousemove', onMove)
    document.removeEventListener('mouseup', onUp)
  }
  document.addEventListener('mousemove', onMove)
  document.addEventListener('mouseup', onUp)
}

function finishDrag() {
  const threshold = slideWidth.value * 0.3
  const off = dragOffset.value
  dragOffset.value = 0

  if (off < -threshold) {
    sliding.value = true
    virtualIndex.value += 1
  } else if (off > threshold) {
    sliding.value = true
    virtualIndex.value -= 1
  }
  startAutoPlay()
}

// ---- 无限循环：过渡结束后无缝跳转 ----
function onTransitionEnd() {
  if (dragging.value || jumping.value) return
  if (virtualIndex.value === 0) {
    jumping.value = true
    requestAnimationFrame(() => {
      virtualIndex.value = slides.length
      requestAnimationFrame(() => {
        jumping.value = false
        sliding.value = false
      })
    })
  } else if (virtualIndex.value === LAST) {
    jumping.value = true
    requestAnimationFrame(() => {
      virtualIndex.value = 1
      requestAnimationFrame(() => {
        jumping.value = false
        sliding.value = false
      })
    })
  } else {
    sliding.value = false
  }
}

// ---- 自动播放 ----
let timer: ReturnType<typeof setInterval> | null = null

function startAutoPlay() {
  stopAutoPlay()
  timer = setInterval(() => {
    if (!dragging.value && !sliding.value) {
      sliding.value = true
      virtualIndex.value += 1
    }
  }, 2000)
}

function stopAutoPlay() {
  if (timer) { clearInterval(timer); timer = null }
}

function goTo(index: number) {
  stopAutoPlay()
  sliding.value = true
  virtualIndex.value = index + 1
  startAutoPlay()
}

// ---- 生命周期 ----
onMounted(() => {
  if (containerRef.value) containerWidth.value = containerRef.value.offsetWidth
  startAutoPlay()
  window.addEventListener('resize', () => {
    if (containerRef.value) containerWidth.value = containerRef.value.offsetWidth
  })
})

onUnmounted(() => stopAutoPlay())
</script>
