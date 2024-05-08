<script setup lang="ts">
import { ref } from 'vue'
import { Button } from '@/components/ui/button'
import { Separator } from '@/components/ui/separator'
import PictureSelector from '@/components/PictureSelector.vue'

// dataURI results
const items = ref<string[]>([''])

const increase = () => {
  items.value.push('')
}

interface CallbackRes {
  url: string
  index: number
}

const callback = ({ url, index }: CallbackRes) => {
  items.value[index] = url
}
</script>

<template>
  <div class="flex flex-col gap-12 w-full min-h-screen box-border p-12">
    <div class="flex justify-end w-full">
      <Button @click="increase">Add Image Item</Button>
    </div>

    <Separator />

    <div class="flex flex-wrap gap-24 w-full">
      <div
        class="flex flex-col gap-3 w-64"
        v-for="(item, index) in items"
        :key="index"
      >
        <PictureSelector :index="index" @callback="callback" />

        <div
          v-if="item"
          class="w-64 h-64 flex justify-center items-center bg-black"
        >
          <img class="max-w-full max-h-full" :src="item" alt="" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
