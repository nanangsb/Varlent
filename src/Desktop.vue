<script setup lang="ts">
import { useDark } from './use'

const iframeRef = ref<HTMLIFrameElement>()

const { isDark, updateTheme } = useDark()

window.addEventListener('message', (event) => {
  if (event.data?.type === 'router-channel-connected') {
    notify()
  }

  if (event.data?.type === 'route-change') {
    window.history.replaceState(null, '', '#' + event.data.path)
  }

  if (event.data?.type === 'theme-change') {
    isDark.value = event.data?.isDark
    updateTheme()
  }
})

window.addEventListener('hashchange', notify)

function notify() {
  iframeRef.value?.contentWindow?.postMessage({ type: 'route-change', path: window.location.hash.slice(1) }, '*')
}
</script>

<template>
  <div class="w-screen h-screen flex justify-center items-center">
    <iframe ref="iframeRef" style="width: 500px" class="h-full border-none" src="./index.html"></iframe>
  </div>
</template>
