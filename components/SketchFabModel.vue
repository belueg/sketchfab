<template>
  <div v-html="data?.html"></div>
</template>

<script lang="ts" setup>
import type { OEmbedResponse } from '~/ts/interfaces'

const { data } = await useFetch<OEmbedResponse>(
  'https://sketchfab.com/oembed?url=https://sketchfab.com/models/dGUrytaktlDeNudCEGKk31oTJY&maxwidth=1041'
)
</script>
