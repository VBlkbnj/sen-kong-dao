<template>
  <!-- 底部固定背景图（Teleport 到 body，z 轴最底层） -->
  <Teleport to="body">
    <div
      class="fixed inset-0"
      style="
        z-index: -1;
        -webkit-mask-image: linear-gradient(
          to bottom,
          black 0%,
          black 80%,
          transparent 100%
        );
        mask-image: linear-gradient(
          to bottom,
          black 0%,
          black 80%,
          transparent 100%
        );
      ">
      <img
        src="https://t.alcy.cc/fj"
        class="w-full h-full object-cover object-center"
        alt="" />
    </div>
  </Teleport>

  <div
    class="min-h-screen"
    :style="{ paddingTop: tabBarHeight + 'px' }"
    @touchstart="onPullStart"
    @touchmove="onPullMove"
    @touchend="onPullEnd">
    <!-- ========== 顶部 Tab 栏（不受下拉影响） ========== -->
    <header
      ref="tabBarRef"
      class="fixed top-0 left-0 right-0 z-20 bg-white/80 backdrop-blur-sm">
      <div class="flex items-center gap-2 px-3 py-2 overflow-x-auto">
        <button
          v-for="tab in tabs"
          :key="tab"
          class="flex-shrink-0 rounded-[8px] px-4 py-2 text-sm border border-transparent transition-colors"
          :class="
            tab === activeTab
              ? 'bg-black/50 text-white'
              : 'bg-black/50 text-white/60'
          ">
          {{ tab }}
        </button>
        <button
          class="flex-shrink-0 w-8 h-8 rounded-lg bg-gray-100 flex items-center justify-center ml-auto">
          <div class="w-3 h-3 bg-gray-300 rounded-sm"></div>
        </button>
        <button
          class="flex-shrink-0 w-8 h-8 rounded-lg flex items-center justify-center"
          @click="router.push('/search')">
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

    <!-- 刷新指示器（不受下拉影响） -->
    <div
      class="fixed left-0 right-0 z-30 flex items-center justify-center"
      :style="{
        top: tabBarHeight + 'px',
        height: pullDistance + 'px',
        overflow: 'hidden',
      }">
      <Icon
        v-if="isRefreshing"
        icon="mdi:loading"
        class="size-6 text-gray-500 animate-spin" />
      <Icon
        v-else-if="pullDistance > 40"
        icon="mdi:arrow-down"
        class="size-6 text-gray-500" />
    </div>

    <!-- ====== 可下拉的内容区（轮播图 + 瀑布流） ====== -->
    <div
      :style="{
        transform: `translateY(${pullDistance}px)`,
        transition: pulling ? 'none' : 'transform 0.3s ease-out',
      }">
      <!-- 轮播图 -->
      <div
        ref="carouselWrapRef"
        class="fixed left-0 right-0 z-0"
        :style="{ top: '0px', opacity: carouselOpacity }">
        <CarouselBanner />
      </div>

      <!-- 占位块 -->
      <div :style="{ height: carouselHeight + 'px' }" />

      <!-- 瀑布流 -->
      <main class="relative z-10 px-2 pb-[10vh]">
        <div class="columns-2 gap-2 space-y-2">
          <div
            v-for="(card, idx) in cards"
            :key="idx"
            class="break-inside-avoid bg-white rounded-xl overflow-hidden shadow-sm mb-2 cursor-pointer"
            @click="openDetail(card)">
            <div
              class="relative w-full bg-gray-200"
              :style="{
                height: imageHeights[idx % imageHeights.length] + 'px',
              }">
              <div
                v-if="card.type !== 'article'"
                class="absolute top-2 right-2 w-7 h-7 rounded-full bg-black/50 flex items-center justify-center">
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
                <svg
                  v-else-if="card.type === 'video'"
                  class="w-3.5 h-3.5 text-white ml-0.5"
                  viewBox="0 0 10 12"
                  fill="white">
                  <path d="M0 0L10 6L0 12Z" />
                </svg>
              </div>
            </div>

            <div class="px-3 pt-2">
              <p class="text-xs text-gray-800 leading-relaxed truncate">
                {{ card.title }}
              </p>
            </div>

            <div class="flex items-end gap-2 px-3 py-2.5">
              <div class="w-7 h-7 rounded-full bg-gray-300 flex-shrink-0"></div>
              <div class="flex-1 min-w-0">
                <div class="text-xs font-medium text-gray-800 truncate">
                  {{ card.author }}
                </div>
              </div>
              <button
                class="flex items-center gap-0.5 text-gray-400"
                @click.stop="toggleLike(card)">
                <Icon
                  :icon="card.liked ? 'mdi:heart' : 'mdi:heart-outline'"
                  class="w-4 h-4"
                  :class="card.liked ? 'text-red-500' : 'text-gray-400'" />
                <span
                  class="text-[10px]"
                  :class="card.liked ? 'text-red-500' : 'text-gray-400'"
                  >{{ card.likes }}</span
                >
              </button>
              <button class="flex items-center gap-0.5 text-gray-400">
                <Icon icon="mdi:comment-outline" class="w-3.5 h-3.5" />
                <span class="text-[10px] text-gray-400">{{
                  card.comments
                }}</span>
              </button>
            </div>
          </div>
        </div>
      </main>
    </div>

    <BottomNav active="home" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, nextTick } from "vue";
import { Icon } from "@iconify/vue";

const router = useRouter();
const tabs = ["推荐", "热门", "最新", "附近"];
const activeTab = ref("推荐");

const imageHeights = [176, 229, 158, 194, 246, 141, 211, 264];
const cardTypes = ["image-set", "video", "article"] as const;

const authors = ["明日方舟", "星穹铁道", "原神", "绝区零", "鸣潮"];
const titles = [
  "新版本更新内容一览，这些改动你必须知道",
  "每日一图分享｜风景这边独好",
  "新手入门指南：从零开始的冒险",
  "限时活动攻略，手把手教你拿满奖励",
  "角色评测：值得培养的强力角色推荐",
];
const times = ["3分钟前", "15分钟前", "1小时前", "2小时前", "5小时前"];

const cards = ref(
  Array.from({ length: 10 }, (_, i) => ({
    id: i,
    type: cardTypes[i % cardTypes.length],
    author: authors[i % authors.length],
    title: titles[i % titles.length],
    time: times[i % times.length],
    comments: Math.floor(Math.random() * 999),
    likes: Math.floor(Math.random() * 9999),
    liked: false,
  })),
);

function toggleLike(card: (typeof cards.value)[number]) {
  card.liked = !card.liked;
  card.likes += card.liked ? 1 : -1;
}

const typeMap: Record<string, string> = {
  "image-set": "image",
  video: "video",
  article: "article",
};

function openDetail(card: (typeof cards.value)[number]) {
  router.push({ path: "/detail", query: { type: typeMap[card.type] } });
}

// ---- 下拉刷新 ----
const pulling = ref(false);
const pullDistance = ref(0);
const isRefreshing = ref(false);
let pullStartY = 0;

function onPullStart(e: TouchEvent) {
  if (window.scrollY > 5) return;
  if (isRefreshing.value) return;
  pulling.value = true;
  pullStartY = e.touches[0].clientY;
  pullDistance.value = 0;
}

function onPullMove(e: TouchEvent) {
  if (!pulling.value) return;
  const dy = e.touches[0].clientY - pullStartY;
  if (dy > 0) {
    pullDistance.value = Math.min(dy * 0.4, 120);
  }
}

function onPullEnd() {
  if (!pulling.value) return;
  pulling.value = false;
  if (pullDistance.value > window.innerHeight * 0.1) {
    isRefreshing.value = true;
    pullDistance.value = 60;
    // 模拟刷新
    setTimeout(() => {
      isRefreshing.value = false;
      pullDistance.value = 0;
    }, 1200);
  } else {
    pullDistance.value = 0;
  }
}

// ---- 动效 ----
const tabBarRef = ref<HTMLElement | null>(null);
const carouselWrapRef = ref<HTMLElement | null>(null);
const tabBarHeight = ref(0);
const carouselHeight = ref(200);
const carouselOpacity = ref(1);

let rafId = 0;

function onScroll() {
  cancelAnimationFrame(rafId);
  rafId = requestAnimationFrame(() => {
    const scrollY = window.scrollY || document.documentElement.scrollTop;
    const progress = Math.min(Math.max(scrollY / carouselHeight.value, 0), 1);
    carouselOpacity.value = 1 - progress;
  });
}

function measure() {
  if (tabBarRef.value) {
    tabBarHeight.value = tabBarRef.value.offsetHeight;
  }
  // 轮播图：取内部 CarouselBanner 组件根元素的实际高度
  if (carouselWrapRef.value) {
    const inner = carouselWrapRef.value.querySelector(
      ".relative",
    ) as HTMLElement | null;
    if (inner) {
      carouselHeight.value = inner.offsetHeight;
    } else {
      carouselHeight.value = 200;
    }
  }
}

onMounted(async () => {
  await nextTick();
  // 延迟一帧确保子组件渲染完毕
  requestAnimationFrame(() => {
    measure();
    onScroll();
  });

  window.addEventListener("scroll", onScroll, { passive: true });
  window.addEventListener("resize", () => {
    measure();
    onScroll();
  });
});

onUnmounted(() => {
  window.removeEventListener("scroll", onScroll);
  cancelAnimationFrame(rafId);
});
</script>
