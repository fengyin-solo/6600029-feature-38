<script setup lang="ts">
import { useDroneStore } from '../store/drone';

const store = useDroneStore();

const filterOptions = [
  { type: 'airport', label: '机场', activeColor: 'bg-red-600', textColor: 'text-red-400' },
  { type: 'military', label: '军事', activeColor: 'bg-orange-600', textColor: 'text-orange-400' },
  { type: 'restricted', label: '限制区', activeColor: 'bg-purple-600', textColor: 'text-purple-400' },
];

function getTypeLabel(type: string): string {
  const opt = filterOptions.find((o) => o.type === type);
  return opt ? opt.label : type;
}

function getTypeTextColor(type: string): string {
  const opt = filterOptions.find((o) => o.type === type);
  return opt ? opt.textColor : 'text-slate-400';
}

function isFilterActive(type: string): boolean {
  return store.zoneFilter.has(type);
}

function isZoneSelected(id: string): boolean {
  return store.selectedZoneId === id;
}

function onZoneClick(id: string) {
  store.selectZone(isZoneSelected(id) ? null : id);
}
</script>

<template>
  <div class="bg-slate-800 rounded-lg p-4 space-y-3">
    <div class="flex items-center justify-between border-b border-slate-700 pb-2">
      <h3 class="text-sm font-bold text-slate-200">禁飞区列表</h3>
      <button
        @click="store.clearZoneFilter"
        class="text-xs text-slate-400 hover:text-slate-200 underline"
      >
        重置筛选
      </button>
    </div>

    <div class="flex gap-2">
      <button
        v-for="opt in filterOptions"
        :key="opt.type"
        @click="store.toggleZoneFilter(opt.type)"
        class="flex-1 py-1.5 px-2 rounded text-xs font-medium border transition"
        :class="[
          isFilterActive(opt.type)
            ? `${opt.activeColor} text-white border-transparent`
            : 'bg-slate-900 text-slate-400 border-slate-700 hover:border-slate-600'
        ]"
      >
        {{ opt.label }}
      </button>
    </div>

    <ul class="space-y-1 max-h-60 overflow-y-auto">
      <li
        v-for="zone in store.visibleZones"
        :key="zone.id"
        @click="onZoneClick(zone.id)"
        class="cursor-pointer rounded px-2 py-1.5 text-xs transition flex items-center justify-between"
        :class="[
          isZoneSelected(zone.id)
            ? 'bg-slate-600 text-white'
            : 'bg-slate-900/50 text-slate-300 hover:bg-slate-700'
        ]"
      >
        <span class="truncate">{{ zone.name }}</span>
        <span :class="getTypeTextColor(zone.type)">{{ getTypeLabel(zone.type) }}</span>
      </li>
      <li v-if="store.visibleZones.length === 0" class="text-xs text-slate-500 px-2 py-2">
        暂无禁区
      </li>
    </ul>
  </div>
</template>
