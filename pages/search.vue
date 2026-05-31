<template>
  <div class="min-h-screen bg-gray-50">
    <!-- 上部 搜索框 + 取消 -->
    <div class="flex items-center gap-2 px-3 pt-3 pb-2">
      <div class="flex items-center gap-2 bg-gray-100 rounded-[8px] px-3 py-2.5 flex-1">
        <Icon icon="mdi:magnify" class="size-5 text-gray-400 flex-shrink-0" />
        <input
          ref="inputRef"
          v-model="keyword"
          type="text"
          placeholder="搜索"
          class="flex-1 bg-transparent outline-none text-sm text-gray-800 placeholder:text-gray-400"
        />
        <button v-if="keyword" class="flex-shrink-0" @click="keyword = ''">
          <Icon icon="mdi:close-circle" class="size-4 text-gray-400" />
        </button>
      </div>
      <button class="flex-shrink-0 text-sm text-gray-800" @click="goBack">取消</button>
    </div>

    <!-- 中部 -->
    <div class="px-3 space-y-3">
      <!-- 历史搜索 -->
      <div class="bg-white rounded-[8px] px-3 py-3">
        <div class="flex items-center justify-between mb-3">
          <span class="text-sm font-medium text-black">历史搜索</span>
          <button class="text-xs text-black" @click="history = []">
            <Icon icon="mdi:delete-outline" class="size-4" />
          </button>
        </div>
        <div class="flex flex-wrap gap-2">
          <span
            v-for="(h, i) in history"
            :key="i"
            class="px-3 py-1.5 rounded-[8px] bg-gray-100 text-xs text-gray-900 cursor-pointer"
            @click="keyword = h"
          >{{ h }}</span>
          <span
            v-if="history.length === 0"
            class="text-xs text-black"
          >暂无历史搜索</span>
        </div>
      </div>

      <!-- 热门搜索 -->
      <div class="bg-white rounded-[8px] px-3 py-3">
        <div class="mb-3">
          <span class="text-sm font-medium text-black">热门搜索</span>
        </div>
        <div class="flex flex-wrap gap-2">
          <span
            v-for="(t, i) in hotTopics"
            :key="i"
            class="px-3 py-1.5 rounded-[8px] bg-gray-100 text-xs text-black cursor-pointer"
            @click="keyword = t"
          >{{ t }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { Icon } from '@iconify/vue'

const router = useRouter()
const keyword = ref('')
const inputRef = ref<HTMLInputElement | null>(null)

function goBack() {
  router.push('/')
}

const history = ref(['明日方舟新活动', '原神攻略', '角色评测', '限时活动', '新手入门'])

const hotTopics = ['新版本更新', '每日一图', '新手攻略', '限时活动', '强力角色', '深渊阵容', '武器推荐', '材料获取']

onMounted(() => {
  inputRef.value?.focus()
})
</script>
