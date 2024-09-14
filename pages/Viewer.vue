<template>
  <div class="flex-col flex items-center justify-center p-4 h-screen">
    <iframe
      src=""
      id="api-frame"
      ref="iframe"
      allow="autoplay; fullscreen; xr-spatial-tracking"
      class="hidden w-9/12 h-full"
      webkitallowfullscreen="true"
      mozallowfullscreen="true"
      allowfullscreen
    ></iframe>
    <div class="flex justify-around items-center w-9/12 p-2">
      <button
        @click="setBackground"
        class="px-4 py-2 bg-blue-500 text-white font-semibold rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50"
      >
        Toggle Background
      </button>
          <button
        @click="centerCamera"
        class="px-4 py-2 bg-blue-500 text-white font-semibold rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50"
      >
        Center Camera
      </button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'

onMounted(() => loadModel())

const iframe = ref<HTMLIFrameElement | null>(null)
const uid = '645d2a72d8104da39997a698a4650187' // Sketchfab model
const loading = ref(true)
const api = ref<any>(null)
const isBlack = ref<boolean>(false)

const loadModel = async () => {
  if (!iframe.value) return

  const client = new (window as any).Sketchfab(iframe.value)

  try {
    await new Promise<void>((resolve, reject) => {
      client.init(uid, {
        success: function (loadedValue: any) {
          loadedValue.start()

          api.value = loadedValue

          loadedValue.addEventListener('viewerready', function () {
            if (iframe.value) iframe.value.classList.remove('hidden')
            resolve() 
          })
        },
        error: function () {
          reject('Error loading the viewer') 
        },
        camera: 0,
        blending: 1
      })
    })
  } catch (err) {
    console.log(err)
    error.value = true
  } finally {
    loading.value = false
  }
}

const setBackground = () => {
  isBlack.value = !isBlack.value

  if (api.value) {
    const newColor = isBlack.value ? [1, 1, 1] : [0, 0, 0]
    api.value.setBackground({ color: newColor }, function () {
      window.console.log('Background changed', isBlack.value)
    })
  }
}

const centerCamera = () => {
  api.value.recenterCamera(function (err) {
    if (!err) {
      window.console.log('Camera recentered')
    }
  })
}
</script>

<style scoped>
.hidden {
  visibility: hidden;
  height: 0;
  width: 0;
}
</style>
