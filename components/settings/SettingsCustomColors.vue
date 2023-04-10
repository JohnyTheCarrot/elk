<script setup lang="ts">
import { STORAGE_KEY_CUSTOM_COLORS } from '~~/constants'

function readCustomColors() {
  const customColors = localStorage.getItem(STORAGE_KEY_CUSTOM_COLORS) ?? '{}'

  return JSON.parse(customColors)
}

function writeCustomColor(key: string, value: string) {
  const current = readCustomColors()

  const newCustomColors = JSON.stringify({
    ...current,
    [key]: value,
  })

  localStorage.setItem(STORAGE_KEY_CUSTOM_COLORS, newCustomColors)
}

const rgbBgBase = computed({
  get() {
    const customColors = readCustomColors()['rgb-bg-base'] ?? '0, 0, 0'

    const [r, g, b] = customColors.split(', ')

    const rHex = parseInt(r).toString(16).padStart(2, '0')
    const gHex = parseInt(g).toString(16).padStart(2, '0')
    const bHex = parseInt(b).toString(16).padStart(2, '0')

    return `#${rHex}${gHex}${bHex}`
  },
  set(value: string) {
    const hexValue = parseInt(value.slice(1), 16)
    const r = (hexValue & 0xFF_00_00) >> 16
    const g = (hexValue & 0x00_FF_00) >> 8
    const b = hexValue & 0x00_00_FF

    writeCustomColor('rgb-bg-base', `${r}, ${g}, ${b}`)
    window.dispatchEvent(new Event('storage'))
  },
})
</script>

<template>
  <div flex="~ col gap4 wrap">
    <div space-y-2>
      <p font-small>
        Base Color
      </p>
      <input v-model="rgbBgBase" type="color">
    </div>
  </div>
</template>
