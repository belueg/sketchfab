<template>
  <di v-if="loading">loading....</di>
  <div class="flex bg-orange-200 w-full h-screen justify-around p-4">
    <div
      v-show="!loading"
      class="flex-col flex items-center justify-center w-10/12"
    >
      <iframe
        src=""
        id="api-frame"
        ref="iframe"
        allow="autoplay; fullscreen; xr-spatial-tracking"
        class="hidden w-full h-full"
        webkitallowfullscreen="true"
        mozallowfullscreen="true"
        allowfullscreen
      >
      </iframe>
      <div class="flex justify-around items-center w-9/12 p-2">
        <ActionButton @click="setBackground"> Toggle Background </ActionButton>
        <ActionButton @click="centerCamera"> Center Camera </ActionButton>
        <ActionButton @click="takePhoto"> Capture</ActionButton>
      </div>
    </div>
    <div class="w-100 h-screen w-28">
      <img
        v-if="screenshot"
        width="110"
        height="100"
        :src="screenshot"
        alt=""
      />
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
const screenshot = ref(null)

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
    err.value = true
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

const takePhoto = () => {
  api.value.getScreenShot('image/png', function (err, result) {
    if (!err) {
      screenshot.value = result
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
