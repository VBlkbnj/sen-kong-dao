<template>
  <div class="min-h-screen bg-gray-50">
    <!-- ========== 上部 Tab 栏 ========== -->
    <header class="sticky top-0 z-10 bg-white border-b border-gray-100">
      <div class="flex items-center gap-2 px-3 py-2.5 overflow-x-auto">
        <!-- Tab 圆角长方形 (圆角 8px) -->
        <button
          v-for="tab in tabs"
          :key="tab"
          class="flex-shrink-0 rounded-[8px] px-4 py-1.5 text-sm border transition-colors"
          :class="
            tab === activeTab
              ? 'bg-gray-900 text-white border-gray-900'
              : 'bg-gray-100 text-gray-500 border-gray-100'
          ">
          {{ tab }}
        </button>

        <!-- 右侧圆角正方形 -->
        <button
          class="flex-shrink-0 w-8 h-8 rounded-lg bg-gray-100 flex items-center justify-center ml-auto">
          <div class="w-3 h-3 bg-gray-300 rounded-sm"></div>
        </button>

        <!-- 放大镜搜索按钮 -->
        <button
          class="flex-shrink-0 w-8 h-8 rounded-lg flex items-center justify-center">
          <svg
            class="w-5 h-5 text-gray-500"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
        </button>
      </div>
    </header>

    <!-- ========== 上中部 轮播图 ========== -->
    <CarouselBanner />

    <!-- ========== 中部 两列瀑布流 ========== -->
    <main class="px-2 pt-2 pb-[10vh]">
      <div class="columns-2 gap-2 space-y-2">
        <div
          v-for="(card, idx) in cards"
          :key="idx"
          class="break-inside-avoid bg-white rounded-xl overflow-hidden shadow-sm mb-2 cursor-pointer"
          @click="openDetail(card)">
          <!-- 图片占位 -->
          <div
            class="relative w-full bg-gray-200"
            :style="{ height: imageHeights[idx % imageHeights.length] + 'px' }">
            <img
              :src="`https://uapis.cn/api/v1/random/image?seed=${idx}`"
              class="w-full h-full object-cover"
              alt=""
              loading="lazy"
            />
            <!-- 类型图标 -->
            <div
              v-if="card.type !== 'article'"
              class="absolute top-2 right-2 w-7 h-7 rounded-full bg-black/50 flex items-center justify-center">
              <!-- 图片集：方块重叠 -->
              <svg
                v-if="card.type === 'image-set'"
                class="w-4 h-4 text-white"
                viewBox="0 0 16 16"
                fill="none">
                <rect
                  x="1"
                  y="1"
                  width="10"
                  height="10"
                  rx="1.5"
                  stroke="white"
                  stroke-width="1.2"
                  fill="none" />
                <rect
                  x="5"
                  y="5"
                  width="10"
                  height="10"
                  rx="1.5"
                  stroke="white"
                  stroke-width="1.2"
                  fill="white"
                  fill-opacity="0.3" />
              </svg>
              <!-- 视频：三角播放 -->
              <svg
                v-else-if="card.type === 'video'"
                class="w-3.5 h-3.5 text-white ml-0.5"
                viewBox="0 0 10 12"
                fill="white">
                <path d="M0 0L10 6L0 12Z" />
              </svg>
            </div>
          </div>

          <!-- 底部用户信息 -->
          <div class="flex items-center gap-2 px-3 py-3">
            <div class="w-7 h-7 rounded-full bg-gray-300 flex-shrink-0"></div>
            <div class="flex flex-col gap-1">
              <div class="h-3 bg-gray-200 rounded w-16"></div>
              <div class="h-2.5 bg-gray-100 rounded w-10"></div>
            </div>
            <div class="ml-auto flex items-center gap-1">
              <svg
                class="w-4 h-4 text-gray-300"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24">
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
              </svg>
              <div class="h-3 bg-gray-200 rounded w-6"></div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- ========== 下部 固定导航栏 (占屏幕 10%) ========== -->
    <nav
      class="fixed bottom-0 left-0 right-0 z-10 bg-white border-t border-gray-100 h-[10vh]">
      <div class="flex items-center justify-around h-full max-w-lg mx-auto">
        <button
          v-for="item in navItems"
          :key="item.label"
          class="flex flex-col items-center justify-center gap-0.5"
          :class="item.active ? 'text-gray-900' : 'text-gray-400'"
          @click="goNav(item)">
          <!-- Iconify 图标 -->
          <Icon :icon="item.icon" class="size-8" />
          <!-- 文字 -->
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
const tabs = ["推荐", "热门", "最新", "附近"];
const activeTab = ref("推荐");

const navItems = ref([
  { label: "首页", active: true, icon: "mdi:home",            path: "/" },
  { label: "关注", active: false, icon: "mdi:heart-outline",  path: "/follow" },
  { label: "消息", active: false, icon: "mdi:message-outline", path: "/message" },
  { label: "我的", active: false, icon: "mdi:account-outline", path: "/mine" },
])

function goNav(item: typeof navItems.value[number]) {
  if (item.active) return
  router.push(item.path)
};

// 模拟不同高度的图片占位（原值缩小约12%，上部占比从80%→约70%）
const imageHeights = [176, 229, 158, 194, 246, 141, 211, 264];
const cardTypes = ["image-set", "video", "article"] as const;
const cards = Array.from({ length: 10 }, (_, i) => ({
  id: i,
  type: cardTypes[i % cardTypes.length],
}));

const typeMap: Record<string, string> = {
  "image-set": "image",
  video: "video",
  article: "article",
};

function openDetail(card: (typeof cards)[number]) {
  router.push({ path: "/detail", query: { type: typeMap[card.type] } });
}
</script>
