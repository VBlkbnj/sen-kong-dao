<template>
  <div class="min-h-screen bg-gray-50">
    <!-- ========== 中部 ========== -->
    <main class="pb-[10vh]">
      <!-- 右上角按钮 -->
      <div class="flex items-center justify-end gap-3 px-4 pt-3 pb-1">
        <button class="w-8 h-8 flex items-center justify-center">
          <svg class="w-5 h-5 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M12 4v1m6 11h2m-6 0h-2m4-7a3 3 0 11-6 0 3 3 0 016 0z" />
          </svg>
        </button>
        <button class="w-8 h-8 flex items-center justify-center">
          <svg class="w-5 h-5 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.066 2.573c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.573 1.066c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.066-2.573c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
          </svg>
        </button>
      </div>

      <!-- 用户信息卡片 -->
      <div class="flex items-center gap-4 px-4 py-4">
        <div class="w-20 h-20 rounded-full bg-gray-400 flex-shrink-0"></div>
        <div class="flex flex-col gap-1">
          <div class="h-5 bg-gray-200 rounded w-24"></div>
          <div class="h-3 bg-gray-100 rounded w-16"></div>
        </div>
      </div>

      <!-- 数据卡片：获赞/发布/关注/粉丝 -->
      <div class="mx-3 bg-white rounded-[8px] overflow-hidden">
        <div class="flex items-center justify-around py-4">
          <div v-for="item in stats" :key="item.label" class="flex flex-col items-center gap-1">
            <span class="text-base font-bold text-gray-900">{{ item.value }}</span>
            <span class="text-xs text-gray-400">{{ item.label }}</span>
          </div>
        </div>
      </div>

      <!-- 两个并排卡片 -->
      <div class="flex gap-3 px-3 mt-3">
        <div class="flex-1 bg-white rounded-[8px] flex items-center gap-2 px-3 h-20">
          <Icon icon="mdi:clipboard-list-outline" class="size-6 text-gray-500 flex-shrink-0" />
          <span class="text-sm text-gray-600">任务中心</span>
        </div>
        <div class="flex-1 bg-white rounded-[8px] flex items-center gap-2 px-3 h-20">
          <Icon icon="mdi:ticket-percent-outline" class="size-6 text-gray-500 flex-shrink-0" />
          <span class="text-sm text-gray-600">活动中心</span>
        </div>
      </div>

      <!-- Grid 菜单 4列 -->
      <div class="mx-3 mt-3 bg-white rounded-[8px] overflow-hidden">
        <div class="grid border-gray-200" style="grid-template-columns: repeat(4, 1fr)">
          <div
            v-for="item in menuItems"
            :key="item.label"
            class="flex flex-col items-center justify-center gap-1.5 py-4"
          >
            <Icon :icon="item.icon" class="w-6 h-6 text-gray-500" />
            <span class="text-xs text-gray-600">{{ item.label }}</span>
          </div>
        </div>
      </div>
    </main>

    <!-- ========== 下部 导航栏 ========== -->
    <nav class="fixed bottom-0 left-0 right-0 z-10 bg-white border-t border-gray-100 h-[10vh]">
      <div class="flex items-center justify-around h-full max-w-lg mx-auto">
        <button
          v-for="item in navItems"
          :key="item.label"
          class="flex flex-col items-center justify-center gap-0.5"
          :class="item.active ? 'text-gray-900' : 'text-gray-400'"
          @click="goNav(item)"
        >
          <Icon :icon="item.icon" class="size-8" />
          <span class="text-xs">{{ item.label }}</span>
        </button>
      </div>
    </nav>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { Icon } from '@iconify/vue'

const router = useRouter()

const stats = [
  { label: '获赞', value: '128' },
  { label: '发布', value: '36' },
  { label: '关注', value: '52' },
  { label: '粉丝', value: '1.2k' },
]

const menuItems = [
  { label: '我的评论', icon: 'mdi:comment-text-outline' },
  { label: '我的收藏', icon: 'mdi:bookmark-outline' },
  { label: '历史记录', icon: 'mdi:history' },
  { label: '草稿箱',   icon: 'mdi:file-document-edit-outline' },
  { label: '我的角色', icon: 'mdi:account-star-outline' },
  { label: '游戏中心', icon: 'mdi:gamepad-variant-outline' },
  { label: '创作中心', icon: 'mdi:brush' },
  { label: '客服中心', icon: 'mdi:headset' },
  { label: '我的奖品', icon: 'mdi:gift-outline' },
]

const navItems = ref([
  { label: '首页', active: false, icon: 'mdi:home-outline',      path: '/' },
  { label: '关注', active: false, icon: 'mdi:heart-outline',     path: '/follow' },
  { label: '消息', active: false, icon: 'mdi:message-outline',   path: '/message' },
  { label: '我的', active: true,  icon: 'mdi:account',           path: '/mine' },
])

function goNav(item: typeof navItems.value[number]) {
  if (item.active) return
  router.push(item.path)
}
</script>
