<script setup lang="ts">
defineProps({
  performance: Number,
  accessibility: Number,
  bestPractices: Number,
  seo: Number,
  loading: Boolean
})

const radius = 11
const stroke = 2
const normalizedRadius = radius - stroke * 2
const circumference = normalizedRadius * 2 * Math.PI
</script>

<template>
  <div class="flex flex-row flex-wrap gap-4 lg:flex-row justify-around w-full border border-green-700 border-2 rounded-lg p-4">
    <span v-for="[metric, caption] of ([[performance, 'performance'], [accessibility, 'accessibility'], [bestPractices, 'best practices'], [seo, 'SEO']] as const)" class="flex flex-row gap-2 flex-[40%] md:flex-auto">
      <svg v-if="loading" :height="radius * 2" :width="radius * 2" class="animate-spin">
        <circle
          stroke="#6b7280"
          fill="transparent"
          class="transform-origin-center"
          :stroke-dasharray="circumference + ' ' + circumference"
          :style="{ strokeDashoffset: circumference - 80 / 100 * circumference }"
          :stroke-width="stroke"
          :r="normalizedRadius"
          :cx="radius"
          :cy="radius"
        />
      </svg>
      <span v-else class="font-bold tabular-nums min-w-[2.25rem] md:min-w-0 text-right mr-1">{{ loading ? undefined : metric?.toFixed(0) }}</span>
      <span>
        {{ caption }}
      </span>
    </span>
  </div>
</template>
