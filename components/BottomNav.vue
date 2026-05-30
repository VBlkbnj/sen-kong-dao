<template>
  <!-- 发布弹窗遮罩 + 浮动发布按钮 + 动作按钮 -->
  <Teleport to="body">
    <!-- 白色半透明高斯模糊遮罩 -->
    <Transition name="overlay-fade">
      <div
        v-if="showOverlay"
        class="fixed inset-0 z-20 bg-white/50 backdrop-blur-md"
        @click="closeOverlay"
      />
    </Transition>

    <!-- 浮动发布按钮：独自在遮罩上方 -->
    <Transition name="publish-float">
      <button
        v-if="showOverlay"
        class="fixed z-50 rounded-full bg-lime-400 text-gray-900 flex items-center justify-center shadow-md"
        style="width: 53px; height: 53px; left: 90%; bottom: 56px; transform: translate(-50%, 50%);"
        @click="closeOverlay"
      >
        <span class="icon-anim block w-6 h-6">
          <Icon icon="mdi:close" class="size-6" />
        </span>
      </button>
    </Transition>

    <!-- 三个上浮动作按钮 -->
    <div
      v-if="showOverlay"
      class="fixed z-40 flex justify-around px-10"
      style="left: 0; right: 0; bottom: 106px;"
    >
      <button
        v-for="(btn, i) in actionBtns"
        :key="btn.label"
        class="action-float-btn flex flex-col items-center gap-2"
        :style="{ animationDelay: `${i * 80}ms` }"
      >
        <div class="w-[76px] h-[76px] rounded-full bg-white shadow-lg flex items-center justify-center">
          <Icon :icon="btn.icon" class="size-8 text-gray-700" />
        </div>
        <span class="text-xs text-gray-600">{{ btn.label }}</span>
      </button>
    </div>

    <!-- 草稿箱按钮：居中，与发布按钮横向中轴线重合 -->
    <div
      v-if="showOverlay"
      class="fixed z-40 left-0 right-0 flex justify-center"
      style="bottom: 28px;"
    >
      <button
        class="action-float-btn flex items-center gap-3 px-6 h-14 rounded-full bg-white shadow-lg text-gray-700"
        style="animation-delay: 240ms;"
      >
        <Icon icon="mdi:archive-outline" class="size-5 flex-shrink-0" />
        <span class="text-sm">草稿箱</span>
      </button>
    </div>
  </Teleport>

  <!-- ==================== 导航栏本体（保持在遮罩下层） ==================== -->
  <nav class="fixed bottom-0 left-0 right-0 z-10" style="padding-bottom: env(safe-area-inset-bottom, 0);">
    <!-- 白色背景 + mask 挖出圆形缺口 -->
    <div
      class="bg-white w-full h-14"
      style="
        -webkit-mask-image: radial-gradient(circle 36px at 90% 0px, transparent 100%, black 100%);
        mask-image: radial-gradient(circle 36px at 90% 0px, transparent 100%, black 100%);
      "
    />

    <!-- 内容层 -->
    <div class="flex h-14 absolute bottom-0 left-0 right-0">
      <!-- 四个导航图标 -->
      <div class="w-4/5 h-full flex items-center justify-around">
        <button
          v-for="item in navItems"
          :key="item.label"
          class="flex flex-col items-center justify-center gap-0.5 h-full w-full"
          :class="item.active ? 'text-gray-900' : 'text-gray-400'"
          @click="goNav(item)"
        >
          <Icon :icon="item.icon" class="size-7" />
          <span class="text-xs">{{ item.label }}</span>
        </button>
      </div>

      <!-- 关闭态发布按钮 -->
      <button
        v-show="!showOverlay"
        class="absolute w-[60px] h-[60px] rounded-full bg-lime-400 text-gray-900 flex items-center justify-center shadow-md"
        style="left: 90%; top: 0px; transform: translate(-50%, -50%);"
        @click="toggleOverlay"
      >
        <Icon icon="mdi:feather" class="size-7" />
      </button>
    </div>
  </nav>
</template>

<script setup lang="ts">
import { ref, computed, watch, onUnmounted } from 'vue'
import { Icon } from '@iconify/vue'

const props = defineProps<{
  active: 'home' | 'follow' | 'message' | 'mine'
}>()

const emit = defineEmits<{
  publish: []
}>()

const router = useRouter()

// ---- 发布弹窗状态 ----
const showOverlay = ref(false)

// 遮罩打开时锁定页面滚动
watch(showOverlay, (val) => {
  document.body.style.overflow = val ? 'hidden' : ''
})

onUnmounted(() => {
  document.body.style.overflow = ''
})

function toggleOverlay() {
  showOverlay.value = !showOverlay.value
}

function closeOverlay() {
  showOverlay.value = false
}

// ---- 三个动作按钮 ----
const actionBtns = [
  { label: '发图文', icon: 'mdi:image-text' },
  { label: '发图集', icon: 'mdi:image-outline' },
  { label: '发视频', icon: 'mdi:video-outline' },
]

const navItems = computed(() => [
  { label: '首页', active: props.active === 'home',    icon: 'mdi:home-outline',      path: '/' },
  { label: '关注', active: props.active === 'follow',   icon: 'mdi:heart-outline',     path: '/follow' },
  { label: '消息', active: props.active === 'message',  icon: 'mdi:message-outline',   path: '/message' },
  { label: '我的', active: props.active === 'mine',     icon: 'mdi:account-outline',   path: '/mine' },
])

function goNav(item: typeof navItems.value[number]) {
  if (item.active) return
  router.push(item.path)
}

function onPublish() {
  emit('publish')
}
</script>

<style scoped>
/* ====== 遮罩淡入淡出 ====== */
.overlay-fade-enter-active,
.overlay-fade-leave-active {
  transition: opacity 0.3s ease;
}
.overlay-fade-enter-from,
.overlay-fade-leave-to {
  opacity: 0;
}

/* ====== 浮动按钮弹出动画（弹性缩放 + 旋转） ====== */
.publish-float-enter-active {
  transition: all 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
}
.publish-float-leave-active {
  transition: all 0.2s ease-in;
}
.publish-float-enter-from {
  opacity: 0;
  transform: translate(-50%, 50%) scale(0.3);
}
.publish-float-leave-to {
  opacity: 0;
  transform: translate(-50%, 50%) scale(0.3);
}

/* ====== 图标弹入动画 ====== */
.publish-float-enter-active .icon-anim :deep(svg) {
  animation: icon-spin-in 0.4s cubic-bezier(0.4, 0, 0.2, 1) both;
}

@keyframes icon-spin-in {
  from {
    opacity: 0;
    transform: rotate(-135deg) scale(0.3);
  }
  to {
    opacity: 1;
    transform: rotate(0deg) scale(1);
  }
}

/* ====== 动作按钮：上浮 + 淡入 ====== */
.action-float-btn {
  opacity: 0;
  transform: translateY(40px);
  animation: float-up 0.35s ease-out forwards;
}

@keyframes float-up {
  from {
    opacity: 0;
    transform: translateY(40px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
