<script setup lang="ts">
import { joinURL } from 'ufo'

const props = defineProps({
  domain: String,
  timestamp: Number,
  type: String as () => 'crux' | 'pagespeed-insights',
})

const canonicalURL = computed(() => props.domain ? joinURL(`https://page-speed.dev`, props.domain) : 'https://page-speed.dev')
const shareLink = computed(() => props.domain ? `https://twitter.com/intent/tweet?text=${encodeURIComponent(`Check out the Page Speed results for ${props.domain.replace(/\./g, '.​')}` + `\n\nhttps://page-speed.dev/${props.domain}`)}` : 'See and share PageSpeed Insights results simply and easily.')

async function nativeShare () {
  if (!props.domain) { return }
  try {
    if (navigator.share) {
      return await navigator.share({
        title: 'page-speed.dev',
        text: `See page speed results for ${props.domain.replace(/\./g, '.​')}`,
        url: canonicalURL.value,
      })
    }
  } catch {
    // ignore errors sharing to native share and fall back directly to Twitter
  }
  return await navigateTo(shareLink.value, { external: true, open: { target: '_blank' } })
}
</script>

<template>
  <div class="flex flex-col gap-2 mt-auto md:mt-8">
    <NuxtLink type="submit"
      class="bg-green-400 text-black hover: hover:bg-white focus:bg-white active:bg-white text-xl md:text-2xl py-2 px-6 md:self-start mb-8"
      :href="shareLink" @click.prevent="nativeShare">
      Share results
    </NuxtLink>
    <a v-if="type === 'crux'"
      :href="`https://lookerstudio.google.com/c/u/0/reporting/bbc5698d-57bb-4969-9e07-68810b9fa348/page/keDQB?params=%7B%22origin%22:%22https://${domain}%22%7D`"
      class="self-start underline text-gray-400 hover:text-green-400 focus:text-green-400 active:text-green-400">
      Explore full results in the CrUX Dashboard &raquo;
    </a>
    <a v-else :href="`https://pagespeed.web.dev/analysis?url=https://${domain}`"
      class="self-start underline text-gray-400 hover:text-green-400 focus:text-green-400 active:text-green-400">
      See full results on PageSpeed Insights &raquo;
    </a>
    <span v-if="timestamp" class="text-gray-400">
      Last updated at
      <NuxtTime :datetime="timestamp" dateStyle="full" timeStyle="medium" />.
    </span>
  </div>
</template>
