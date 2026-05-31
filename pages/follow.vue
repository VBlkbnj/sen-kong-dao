<template>
  <!-- 背景图（Teleport 到 body） -->
  <Teleport to="body">
    <div
      class="fixed inset-0"
      style="z-index: -1;"
    >
      <img
        src="https://t.alcy.cc/fj"
        class="w-full h-full object-cover object-center"
        alt=""
      />
    </div>
  </Teleport>

  <div class="min-h-screen" :style="{ paddingTop: headerHeight + 'px' }">
    <!-- ========== 上部 标题栏（fixed 固定） ========== -->
    <header ref="headerRef" class="fixed top-0 left-0 right-0 z-20 bg-gray-100/80 backdrop-blur-sm">
      <div class="flex items-center justify-center py-2.5">
        <span class="text-base font-bold text-white">关注</span>
      </div>
    </header>

    <!-- ========== 中部 单列瀑布流 ========== -->
    <main class="relative z-10 px-3 py-3 pb-[10vh]">
      <div class="flex flex-col gap-3">
        <div
          v-for="(card, idx) in cards"
          :key="idx"
          class="bg-white rounded-[8px] overflow-hidden shadow-sm cursor-pointer"
          @click="openDetail(card)"
        >
          <!-- 卡片上部：用户信息 -->
          <div class="flex items-center gap-2 px-3 pt-3 pb-2">
            <div class="w-8 h-8 rounded-full bg-gray-300 flex-shrink-0"></div>
            <div class="flex flex-col gap-0.5 flex-1">
              <div class="h-3.5 bg-gray-200 rounded w-20"></div>
              <div class="h-2.5 bg-gray-100 rounded w-16"></div>
            </div>
            <button class="w-7 h-7 flex items-center justify-center">
              <svg class="w-4 h-4 text-gray-400" fill="currentColor" viewBox="0 0 24 24">
                <circle cx="5" cy="12" r="2" /><circle cx="12" cy="12" r="2" /><circle cx="19" cy="12" r="2" />
              </svg>
            </button>
          </div>

          <!-- 卡片中部：内容预览 -->
          <div class="px-3">
            <div v-if="card.type === 'article'" class="space-y-1.5 py-1">
              <div class="h-3 bg-gray-100 rounded w-full"></div>
              <div class="h-3 bg-gray-100 rounded w-5/6"></div>
              <div class="h-3 bg-gray-100 rounded w-3/4"></div>
            </div>
            <div v-else-if="card.type === 'image'" class="bg-gray-200 rounded-md flex items-center justify-center" style="height: 160px">
              <div class="text-gray-400 text-xs">图片内容</div>
            </div>
            <div v-else class="relative bg-gray-800 rounded-md flex items-center justify-center" style="height: 160px">
              <div class="w-10 h-10 rounded-full bg-white/30 flex items-center justify-center">
                <svg class="w-4 h-4 text-white ml-0.5" viewBox="0 0 10 12" fill="white">
                  <path d="M0 0L10 6L0 12Z"/>
                </svg>
              </div>
            </div>
            <div class="pt-2 pb-1">
              <div class="h-4 bg-gray-200 rounded w-3/4"></div>
            </div>
          </div>

          <!-- 卡片下部：操作按钮 -->
          <div class="flex items-center justify-between px-3 py-2.5 border-t border-gray-50">
            <button class="flex items-center gap-1 text-gray-400">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.368 2.684 3 3 0 00-5.368-2.684z" />
              </svg>
            </button>
            <div class="flex items-center gap-4 text-gray-400 text-xs">
              <span class="flex items-center gap-1">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z" />
                </svg>
                <span>{{ card.comments }}</span>
              </span>
              <button class="flex items-center gap-1" @click.stop="toggleLike(card)">
                <Icon
                  :icon="card.liked ? 'mdi:heart' : 'mdi:heart-outline'"
                  class="w-3.5 h-3.5"
                  :class="card.liked ? 'text-red-500' : 'text-gray-400'"
                />
                <span>{{ card.likes }}</span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </main>

    <BottomNav active="follow" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick } from 'vue'
import { Icon } from '@iconify/vue'

const router = useRouter()

function openDetail(card: typeof cards.value[number]) {
  router.push({ path: '/detail', query: { type: card.type } })
}

const cardTypes = ['article', 'image', 'video'] as const
const cards = ref(Array.from({ length: 8 }, (_, i) => ({
  id: i,
  type: cardTypes[i % cardTypes.length],
  comments: Math.floor(Math.random() * 999),
  likes: Math.floor(Math.random() * 9999),
  liked: false,
})))

function toggleLike(card: typeof cards.value[number]) {
  card.liked = !card.liked
  card.likes += card.liked ? 1 : -1
}

// ---- 固定 header 高度 ----
const headerRef = ref<HTMLElement | null>(null)
const headerHeight = ref(0)

onMounted(async () => {
  await nextTick()
  if (headerRef.value) {
    headerHeight.value = headerRef.value.offsetHeight
  }
})
</script>
