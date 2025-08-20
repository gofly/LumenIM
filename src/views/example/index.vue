<script lang="ts" setup>
import encoder from '@/utils/encoder'

import { uncompress } from 'snappyjs'

const ws = new WebSocket('wss://chat.zlx.pub:31194/wss/default.io')
ws.binaryType = 'arraybuffer'

ws.onmessage = function (e) {
  console.log('Received message:', encoder.decode(new Uint8Array(e.data)))
}
</script>

<template></template>

<style scoped lang="less"></style>
