<template>
  <div class="min-h-screen bg-gray-50 flex flex-col">
    <!-- ========== 上部 Header ========== -->
    <header class="sticky top-0 z-10 bg-white border-b border-gray-100">
      <div class="flex items-center gap-3 px-3 py-2.5">
        <!-- 返回按钮 -->
        <button class="w-8 h-8 flex items-center justify-center flex-shrink-0" @click="goBack">
          <svg class="w-5 h-5 text-gray-900" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
          </svg>
        </button>

        <!-- 用户头像 + 名字 -->
        <div class="flex items-center gap-2 flex-1 min-w-0">
          <div class="w-8 h-8 rounded-full bg-gray-300 flex-shrink-0"></div>
          <div class="h-3.5 bg-gray-200 rounded w-20"></div>
        </div>

        <!-- 设置按钮 -->
        <button class="w-8 h-8 flex items-center justify-center flex-shrink-0">
          <svg class="w-5 h-5 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <circle cx="5" cy="12" r="2" /><circle cx="12" cy="12" r="2" /><circle cx="19" cy="12" r="2" />
          </svg>
        </button>
      </div>
    </header>

    <!-- ========== 中部 聊天消息 ========== -->
    <main class="flex-1 overflow-y-auto px-3 py-4 space-y-3">
      <div
        v-for="(msg, idx) in messages"
        :key="idx"
        class="flex"
        :class="msg.self ? 'justify-end' : 'justify-start'"
      >
        <!-- 对方消息：头像在左 -->
        <template v-if="!msg.self">
          <div class="w-8 h-8 rounded-full bg-gray-300 flex-shrink-0 mr-2"></div>
          <div class="max-w-[70%]">
            <div class="bg-white rounded-[8px] px-3 py-2 shadow-sm">
              <div class="h-3 bg-gray-100 rounded" :style="{ width: msg.width + 'px' }"></div>
            </div>
          </div>
        </template>

        <!-- 自己消息：气泡在右，头像在更右 -->
        <template v-else>
          <div class="max-w-[70%]">
            <div class="bg-gray-900 text-white rounded-[8px] px-3 py-2">
              <div class="h-3 bg-white/20 rounded" :style="{ width: msg.width + 'px' }"></div>
            </div>
          </div>
          <div class="w-8 h-8 rounded-full bg-gray-700 flex-shrink-0 ml-2"></div>
        </template>
      </div>
    </main>

    <!-- ========== 下部 输入栏 ========== -->
    <footer class="bg-white border-t border-gray-100">
      <div class="flex items-center gap-2 px-3 pt-2 pb-[14px]">
        <!-- 输入框 -->
        <div class="flex-1 h-9 bg-gray-100 rounded-full px-3 flex items-center">
          <span class="text-sm text-gray-400">输入消息...</span>
        </div>
        <!-- 表情按钮 -->
        <button class="w-9 h-9 flex items-center justify-center flex-shrink-0">
          <svg class="w-5 h-5 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M14.828 14.828a4 4 0 01-5.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
        </button>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
const router = useRouter()

function goBack() {
  router.back()
}

// 模拟聊天消息：交替左右，不同长度实现阶梯感
const messages = [
  { self: false, width: 120 },
  { self: true,  width: 80 },
  { self: false, width: 160 },
  { self: true,  width: 140 },
  { self: true,  width: 60 },
  { self: false, width: 100 },
  { self: false, width: 180 },
  { self: true,  width: 90 },
  { self: false, width: 130 },
  { self: true,  width: 110 },
]
</script>
