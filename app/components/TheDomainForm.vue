<script setup lang="ts">
import { parseURL } from 'ufo'

const props = defineProps({ domain: String })
const editing = defineModel('editing', { type: Boolean })

const input = ref<HTMLInputElement>()

function enableEditing() {
  editing.value = true
  watch(input, (input) => {
    if (input) {
      input.value = props.domain || ''
      input.focus()
      input.setSelectionRange(0, input.value.length)
    }
  }, { once: true })
}

function navigateToNewDomain(event: Event) {
  const newDomain = new FormData(event.target as HTMLFormElement).get('domain') as string
  if (!newDomain)
    return

  const host = parseURL(newDomain).host || newDomain
  editing.value = false
  return navigateTo(`/${sanitizeDomain(host)}`)
}
</script>

<template>
  <h1 v-if="domain && !editing">
    <button
      class="bg-transparent"
      type="button"
      @click="enableEditing"
    >
      {{ domain }}
    </button>
  </h1>
  <form
    v-else
    class="flex flex-col gap-4 overflow-hidden"
    @submit.prevent="navigateToNewDomain"
  >
    <label
      class="sr-only"
      for="domain"
    >Enter a domain</label>
    <input
      id="domain"
      ref="input"
      name="domain"
      type="text"
      class="md:-mt-1 rounded-none py-0 bg-transparent outline-none border-b-2 border-b-solid border-transparent focus:border-green-500 focus-visible:outline-none underline-dashed"
      autofocus
      autocomplete="url"
      inputmode="url"
      autocapitalize="none"
      placeholder="enter a domain"
      required
    >
    <button
      type="submit"
      class="bg-green-400 text-black hover: hover:bg-white focus:bg-white active:bg-white text-xl md:text-2xl py-2 px-6 md:self-start"
    >
      see
      results
    </button>
  </form>
</template>

<style>
input:-webkit-autofill {
  -webkit-background-clip: text;
  -webkit-text-fill-color: #ffffff;
  caret-color: #ffffff;
}
</style>
