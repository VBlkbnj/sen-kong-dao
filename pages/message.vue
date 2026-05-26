<template>
  <div class="min-h-screen bg-gray-50">
    <!-- ========== 上部 标题栏 ========== -->
    <header class="sticky top-0 z-10 bg-gray-100 border-b border-gray-200">
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
              <svg
                class="w-6 h-6"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24">
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z" />
              </svg>
              <span class="text-sm">收到评论</span>
            </button>
            <button class="flex flex-col items-center gap-2 text-gray-600">
              <svg
                class="w-6 h-6"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24">
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
              </svg>
              <span class="text-sm">@我的</span>
            </button>
            <button class="flex flex-col items-center gap-2 text-gray-600">
              <svg
                class="w-6 h-6"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24">
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
              </svg>
              <span class="text-sm">赞和收藏</span>
            </button>
            <button class="flex flex-col items-center gap-2 text-gray-600">
              <svg
                class="w-6 h-6"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24">
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
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
            @click="openChat">
            <!-- 头像 -->
            <div class="w-11 h-11 rounded-full bg-gray-300 flex-shrink-0"></div>

            <!-- 中间：名字 + 最后消息 -->
            <div class="flex-1 min-w-0">
              <div class="h-3.5 bg-gray-200 rounded w-20 mb-2"></div>
              <div class="h-3 bg-gray-100 rounded w-32"></div>
            </div>

            <!-- 右上角：日期 -->
            <div class="flex-shrink-0 self-start">
              <div class="h-2.5 bg-gray-100 rounded w-12"></div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- ========== 下部 导航栏 ========== -->
    <nav
      class="fixed bottom-0 left-0 right-0 z-10 bg-white border-t border-gray-100 h-[10vh]">
      <div class="flex items-center justify-around h-full max-w-lg mx-auto">
        <button
          v-for="item in navItems"
          :key="item.label"
          class="flex flex-col items-center justify-center gap-0.5"
          :class="item.active ? 'text-gray-900' : 'text-gray-400'"
          @click="goNav(item)">
          <Icon :icon="item.icon" class="size-8" />
          <span class="text-xs">{{ item.label }}</span>
        </button>
      </div>
    </nav>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { Icon } from "@iconify/vue";

const router = useRouter();

const navItems = ref([
  { label: "首页", active: false, icon: "mdi:home-outline", path: "/" },
  { label: "关注", active: false, icon: "mdi:heart-outline", path: "/follow" },
  { label: "消息", active: true, icon: "mdi:message", path: "/message" },
  { label: "我的", active: false, icon: "mdi:account-outline", path: "/mine" },
]);

function goNav(item: (typeof navItems.value)[number]) {
  if (item.active) return;
  router.push(item.path);
}

function openChat() {
  router.push("/chat");
}

const chatList = Array.from({ length: 6 }, (_, i) => ({ id: i }));
</script>
