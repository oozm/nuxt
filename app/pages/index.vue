<template>
  <div class="min-h-screen bg-slate-950 text-slate-100">
    <header class="max-w-5xl mx-auto px-4 py-6 flex flex-col gap-4 md:flex-row md:items-center md:justify-between">
      <div>
        <h1 class="text-2xl font-bold">UI List</h1>
        <p class="text-sm text-slate-400">
          免费可复制的 CSS/Tailwind 小组件集合
        </p>
      </div>

      <div class="flex gap-2">
        <input
          v-model="q"
          type="text"
          placeholder="搜索按钮、卡片、loader..."
          class="bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm w-56 focus:outline-none focus:ring focus:ring-slate-500"
        />
        <button
          class="px-3 py-2 text-sm rounded-lg border border-slate-700 hover:bg-slate-800"
          @click="fetchElements"
        >
          搜索
        </button>
      </div>
    </header>

    <main class="max-w-5xl mx-auto px-4 pb-10">
      <div class="flex gap-2 mb-4 overflow-x-auto text-sm">
        <button
          v-for="cat in categories"
          :key="cat.value"
          class="px-3 py-1 rounded-full border"
          :class="cat.value === category ? 'bg-slate-100 text-slate-900 border-slate-100' : 'border-slate-700'"
          @click="selectCategory(cat.value)"
        >
          {{ cat.label }}
        </button>
      </div>

      <div v-if="pending" class="text-slate-400 text-sm">
        加载中…
      </div>

      <div v-else class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
        <NuxtLink
          v-for="item in elements"
          :key="item.id"
          :to="`/element/${item.id}`"
        >
          <UiCard :item="item" />
        </NuxtLink>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
const q = ref('');
const category = ref<string | undefined>(undefined);

const categories = [
  { label: '全部', value: undefined },
  { label: '按钮', value: 'button' },
  { label: '卡片', value: 'card' },
  { label: 'Loader', value: 'loader' },
  { label: '开关', value: 'switch' },
];

const { data, pending, refresh } = useFetch('/api/elements', {
  query: computed(() => ({
    q: q.value || undefined,
    category: category.value || undefined,
  })),
});

const elements = computed(() => data.value || []);

function fetchElements() {
  refresh();
}

function selectCategory(val?: string) {
  category.value = val;
  refresh();
}
</script>
