<template>
  <!-- ==================== 视频类详情页 ==================== -->
  <div
    v-if="contentType === 'video'"
    class="h-screen bg-black flex flex-col relative">
    <!-- 上部 Header（透明背景覆盖视频） -->
    <header class="absolute top-0 left-0 right-0 z-10">
      <div class="flex items-center justify-between px-3 py-2.5">
        <button
          class="w-8 h-8 flex items-center justify-center"
          @click="goBack">
          <svg
            class="w-5 h-5 text-white"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M15 19l-7-7 7-7" />
          </svg>
        </button>
        <button class="w-8 h-8 flex items-center justify-center">
          <svg
            class="w-5 h-5 text-white"
            fill="currentColor"
            viewBox="0 0 24 24">
            <circle cx="5" cy="12" r="2" />
            <circle cx="12" cy="12" r="2" />
            <circle cx="19" cy="12" r="2" />
          </svg>
        </button>
      </div>
    </header>

    <!-- 中部 视频 + 标题（填满剩余空间，底部留出操作栏高度） -->
    <div class="flex-1 flex flex-col pb-[56px]">
      <!-- 视频播放区 -->
      <div class="flex-1 w-full flex items-center justify-center">
        <div class="text-gray-500 text-sm">视频播放中</div>
      </div>

      <!-- 视频标题 -->
      <div class="px-4 pt-2">
        <div class="h-5 bg-white/20 rounded w-3/4 mb-1"></div>
        <div class="h-5 bg-white/20 rounded w-1/2"></div>
      </div>
    </div>

    <!-- 下部 信息 + 操作栏（固定底部） -->
    <div
      class="fixed bottom-0 left-0 right-0 px-4 py-3 flex items-center gap-4">
      <!-- 用户信息（左侧） -->
      <div class="flex items-center gap-2 flex-1">
        <div class="w-8 h-8 rounded-full bg-white/30 flex-shrink-0"></div>
        <div class="flex flex-col gap-0.5">
          <div class="h-3 bg-white/20 rounded w-16"></div>
          <div class="h-2.5 bg-white/10 rounded w-12"></div>
        </div>
      </div>

      <!-- 操作按钮（右侧横向排列） -->
      <div class="flex items-center gap-3">
        <!-- 评论区 -->
        <button class="flex flex-col items-center gap-0.5">
          <svg
            class="w-6 h-6 text-white/80"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z" />
          </svg>
        </button>

        <!-- 点赞 -->
        <button class="flex flex-col items-center gap-0.5">
          <svg
            class="w-6 h-6 text-white/80"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
          </svg>
        </button>

        <!-- 收藏 -->
        <button class="flex flex-col items-center gap-0.5">
          <svg
            class="w-6 h-6 text-white/80"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M5 5a2 2 0 012-2h10a2 2 0 012 2v16l-7-3.5L5 21V5z" />
          </svg>
        </button>
      </div>
    </div>
  </div>

  <!-- ==================== 图文 / 图片类详情页 ==================== -->
  <div v-else class="min-h-screen bg-white">
    <!-- 上部 固定 Header -->
    <header class="sticky top-0 z-10 bg-white border-b border-gray-100">
      <div class="flex items-center justify-between px-3 py-2.5">
        <button
          class="w-8 h-8 flex items-center justify-center"
          @click="goBack">
          <svg
            class="w-5 h-5 text-gray-900"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M15 19l-7-7 7-7" />
          </svg>
        </button>
        <button class="w-8 h-8 flex items-center justify-center">
          <svg
            class="w-5 h-5 text-gray-900"
            fill="currentColor"
            viewBox="0 0 24 24">
            <circle cx="5" cy="12" r="2" />
            <circle cx="12" cy="12" r="2" />
            <circle cx="19" cy="12" r="2" />
          </svg>
        </button>
      </div>
    </header>

    <!-- 中部 内容 -->
    <main class="pb-[12vh]">
      <!-- 图片轮播（仅手动滑动） -->
      <div v-if="contentType === 'image'" class="relative w-full overflow-hidden bg-gray-200" style="height: 300px" ref="imageCarouselRef">
        <div
          class="flex h-full"
          :style="imageTrackStyle"
          @touchstart="onImageTouchStart"
          @touchmove="onImageTouchMove"
          @touchend="onImageTouchEnd"
          @mousedown="onImageMouseDown"
        >
          <div
            v-for="(_, i) in imageSlides"
            :key="i"
            class="w-full h-full flex-shrink-0 flex items-center justify-center"
          >
            <div class="text-gray-400 text-sm">图片 {{ i + 1 }} / {{ imageSlides.length }}</div>
          </div>
        </div>
        <!-- 分页指示器 -->
        <div class="absolute bottom-3 left-0 right-0 flex items-center justify-center gap-1.5">
          <div
            v-for="(_, i) in imageSlides"
            :key="i"
            class="rounded-full transition-all"
            :class="i === imageCurrent ? 'w-1.5 h-1.5 bg-gray-900' : 'w-1.5 h-1.5 bg-gray-400/60'"
          />
        </div>
      </div>
      <div class="px-4 pt-4">
        <div class="h-6 bg-gray-200 rounded w-3/4 mb-1"></div>
        <div class="h-6 bg-gray-200 rounded w-1/2"></div>
      </div>
      <div class="flex items-center gap-3 px-4 py-3 border-b border-gray-50">
        <div class="w-9 h-9 rounded-full bg-gray-300 flex-shrink-0"></div>
        <div class="flex flex-col gap-1">
          <div class="h-3.5 bg-gray-200 rounded w-20"></div>
          <div class="h-2.5 bg-gray-100 rounded w-24"></div>
        </div>
      </div>
      <div v-if="contentType === 'article'" class="px-4 py-3">
        <div class="space-y-2">
          <div class="h-3 bg-gray-100 rounded w-full"></div>
          <div class="h-3 bg-gray-100 rounded w-full"></div>
          <div class="h-3 bg-gray-100 rounded w-5/6"></div>
          <div class="h-3 bg-gray-100 rounded w-full"></div>
          <div class="h-3 bg-gray-100 rounded w-4/5"></div>
          <div class="h-3 bg-gray-100 rounded w-full"></div>
          <div class="h-3 bg-gray-100 rounded w-3/4"></div>
        </div>
      </div>
      <div id="comments" class="border-t border-gray-100 mt-3">
        <div class="px-4 py-3 text-sm text-gray-400">评论区</div>
        <div
          v-for="i in 4"
          :key="i"
          class="flex gap-3 px-4 py-3 border-b border-gray-50">
          <div class="w-7 h-7 rounded-full bg-gray-200 flex-shrink-0"></div>
          <div class="flex-1 space-y-1.5">
            <div class="flex items-center gap-2">
              <div class="h-3 bg-gray-200 rounded w-16"></div>
              <div class="h-2.5 bg-gray-100 rounded w-12"></div>
            </div>
            <div class="h-3 bg-gray-100 rounded w-full"></div>
            <div class="h-3 bg-gray-100 rounded w-3/4"></div>
          </div>
        </div>
      </div>
    </main>

    <!-- 下部 操作栏 -->
    <footer
      class="fixed bottom-0 left-0 right-0 z-10 bg-white border-t border-gray-100">
      <div class="flex items-center gap-3 px-3 py-2">
        <div class="flex-1 h-9 bg-gray-100 rounded-full px-3 flex items-center">
          <span class="text-sm text-gray-400">说点什么...</span>
        </div>
        <button
          class="w-9 h-9 flex items-center justify-center"
          @click="scrollToComments">
          <svg
            class="w-5 h-5 text-gray-500"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z" />
          </svg>
        </button>
        <button class="w-9 h-9 flex items-center justify-center">
          <svg
            class="w-5 h-5 text-gray-500"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
          </svg>
        </button>
        <button class="w-9 h-9 flex items-center justify-center">
          <svg
            class="w-5 h-5 text-gray-500"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M5 5a2 2 0 012-2h10a2 2 0 012 2v16l-7-3.5L5 21V5z" />
          </svg>
        </button>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const route = useRoute()
const router = useRouter()

const contentType = ref((route.query.type as string) || 'article')

function goBack() {
  router.back()
}

function scrollToComments() {
  const el = document.getElementById('comments')
  if (el) el.scrollIntoView({ behavior: 'smooth' })
}

// ---- 图片轮播（仅手动） ----
const imageSlides = [0, 1, 2, 3, 4]
const imageCurrent = ref(0)
const imageCarouselRef = ref<HTMLElement | null>(null)
const imageDragging = ref(false)
const imageDragOff = ref(0)
let imageStartX = 0
let imageWidth = 375

const imageTrackStyle = computed(() => {
  const base = -imageCurrent.value * imageWidth
  return {
    transform: `translateX(${base + (imageDragging.value ? imageDragOff.value : 0)}px)`,
    transition: imageDragging.value ? 'none' : 'transform 0.3s ease-out',
  }
})

function onImageTouchStart(e: TouchEvent) {
  imageDragging.value = true
  imageStartX = e.touches[0].clientX
  imageDragOff.value = 0
  if (imageCarouselRef.value) imageWidth = imageCarouselRef.value.offsetWidth
}

function onImageTouchMove(e: TouchEvent) {
  if (!imageDragging.value) return
  imageDragOff.value = e.touches[0].clientX - imageStartX
}

function onImageTouchEnd() {
  if (!imageDragging.value) return
  imageDragging.value = false
  finishImageDrag()
}

function onImageMouseDown(e: MouseEvent) {
  imageDragging.value = true
  imageStartX = e.clientX
  imageDragOff.value = 0
  if (imageCarouselRef.value) imageWidth = imageCarouselRef.value.offsetWidth

  const onMove = (ev: MouseEvent) => {
    if (!imageDragging.value) return
    imageDragOff.value = ev.clientX - imageStartX
  }
  const onUp = () => {
    if (!imageDragging.value) return
    imageDragging.value = false
    finishImageDrag()
    document.removeEventListener('mousemove', onMove)
    document.removeEventListener('mouseup', onUp)
  }
  document.addEventListener('mousemove', onMove)
  document.addEventListener('mouseup', onUp)
}

function finishImageDrag() {
  const threshold = imageWidth * 0.3
  const off = imageDragOff.value
  imageDragOff.value = 0

  if (off < -threshold && imageCurrent.value < imageSlides.length - 1) {
    imageCurrent.value += 1
  } else if (off > threshold && imageCurrent.value > 0) {
    imageCurrent.value -= 1
  }
}
</script>
