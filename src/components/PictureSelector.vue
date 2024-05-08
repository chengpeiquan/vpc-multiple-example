<script setup lang="ts">
import { ref } from 'vue'
import VuePictureCropper, { cropper } from 'vue-picture-cropper'
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from '@/components/ui/dialog'
import { Button } from '@/components/ui/button'

const props = defineProps<{
  index: number
}>()
const emit = defineEmits(['callback'])

const open = ref(false)
const openDialog = () => (open.value = true)
const closeDialog = () => (open.value = false)

const pic = ref<string>('')

const uploadInput = ref<HTMLInputElement | null>(null)

const selectFile = (e: Event) => {
  const { files } = e.target as HTMLInputElement
  if (!files || !files.length) return

  const file = files[0]
  const reader = new FileReader()
  reader.readAsDataURL(file)
  reader.onload = () => {
    pic.value = String(reader.result)
    openDialog()

    if (!uploadInput.value) return
    uploadInput.value.value = ''
  }
}

const getResult = async () => {
  if (!cropper) return
  const blob: Blob | null = await cropper.getBlob()
  if (!blob) return

  const previewUrl = URL.createObjectURL(blob)
  emit('callback', {
    url: previewUrl,
    index: props.index,
  })
  closeDialog()
}

const clear = () => cropper?.clear()
const reset = () => cropper?.reset()
</script>

<template>
  <div>
    <Button class="relative" variant="secondary">
      <span>Select Image</span>

      <input
        class="absolute top-0 left-0 w-full h-full opacity-0 cursor-pointer"
        ref="uploadInput"
        type="file"
        accept="image/jpg, image/jpeg, image/png, image/gif"
        @change="selectFile"
      />
    </Button>

    <Dialog :open="open">
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Edit image</DialogTitle>
          <DialogDescription>
            Make changes to your image here. Click get result when you're done.
          </DialogDescription>
        </DialogHeader>

        <VuePictureCropper
          :boxStyle="{
            width: '100%',
            height: '100%',
            backgroundColor: '#f8f8f8',
            margin: 'auto',
          }"
          :img="pic"
          :options="{
            viewMode: 1,
            dragMode: 'crop',
            aspectRatio: 16 / 9,
          }"
        />

        <DialogFooter>
          <Button @click="clear" variant="secondary">Clear</Button>
          <Button @click="reset" variant="secondary">reset</Button>
          <Button @click="getResult">Get Result</Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  </div>
</template>
