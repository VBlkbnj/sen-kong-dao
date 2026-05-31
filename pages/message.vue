<template>
  <div class="min-h-screen" :style="{ paddingTop: headerHeight + 'px' }">
    <!-- ========== 上部 标题栏（fixed + 毛玻璃） ========== -->
    <header ref="headerRef" class="fixed top-0 left-0 right-0 z-20 bg-white/80 backdrop-blur-sm border-b border-gray-100">
      <div class="flex items-center justify-center py-2.5">
        <span class="text-base font-bold text-gray-900">消息</span>
      </div>
    </header>

    <!-- ========== 中部 ========== -->
    <main class="pb-[10vh]">
      <!-- 操作按钮卡片 -->
      <div class="px-3">
        <div class="mt-3 bg-white rounded-[8px] overflow-hidden">
          <div class="flex items-center justify-around px-2 py-[23px]">
            <button class="flex flex-col items-center gap-2 text-gray-600">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z" />
              </svg>
              <span class="text-sm">收到评论</span>
            </button>
            <button class="flex flex-col items-center gap-2 text-gray-600">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
              </svg>
              <span class="text-sm">@我的</span>
            </button>
            <button class="flex flex-col items-center gap-2 text-gray-600">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
              </svg>
              <span class="text-sm">赞和收藏</span>
            </button>
            <button class="flex flex-col items-center gap-2 text-gray-600">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M18 9v3m0 0v3m0-3h3m-3 0h-3m-2-5a4 4 0 11-8 0 4 4 0 018 0zM3 20a6 6 0 0112 0v1H3v-1z" />
              </svg>
              <span class="text-sm">新增关注</span>
            </button>
          </div>
        </div>
      </div>

      <!-- 聊天消息列表卡片 -->
      <div class="px-3">
        <div class="mx-3 mt-3 bg-white rounded-[8px] overflow-hidden">
          <div
            v-for="(chat, idx) in chatList"
            :key="idx"
            class="flex items-center gap-3 px-3 py-3 cursor-pointer"
            :class="{ 'border-b border-gray-50': idx < chatList.length - 1 }"
            @click="openChat"
          >
            <div class="w-11 h-11 rounded-full bg-gray-300 flex-shrink-0"></div>
            <div class="flex-1 min-w-0">
              <div class="text-sm font-medium text-gray-900 truncate">{{ chat.name }}</div>
              <div class="text-xs text-gray-500 truncate mt-0.5">{{ chat.lastMsg }}</div>
            </div>
            <div class="flex-shrink-0 self-start text-[10px] text-gray-400">{{ chat.time }}</div>
          </div>
        </div>
      </div>
    </main>

    <BottomNav active="message" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick } from 'vue'

const router = useRouter();

function openChat() {
  router.push("/chat");
}

const chatList = [
  { name: '明日方舟', time: '刚刚', lastMsg: '新活动开始了，快来看看吧！' },
  { name: '星穹铁道', time: '5分钟前', lastMsg: '今天更新了2.0版本' },
  { name: '原神官方', time: '1小时前', lastMsg: '你的冒险还顺利吗？' },
  { name: '绝区零', time: '2小时前', lastMsg: '谢谢你的支持~' },
  { name: '鸣潮', time: '昨天', lastMsg: '好的，没问题' },
  { name: '系统通知', time: '3天前', lastMsg: '你有一条新的系统消息' },
]

// ---- 固定 header ----
const headerRef = ref<HTMLElement | null>(null)
const headerHeight = ref(0)

onMounted(async () => {
  await nextTick()
  if (headerRef.value) headerHeight.value = headerRef.value.offsetHeight
})
</script>
