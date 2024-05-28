<template>
  <div class="flex gap-12 w-6/12 my-0 mx-auto">
  <div class=" w-6/12 mt-24">
  <h1 class="text-center font-bold text-3xl">Box 1</h1>
    <div
      class="drop-zone p-7 mt-2.5 bg-teal-100 rounded-md min-h-52"
      @drop.prevent="onDrop($event, 1)"
      @dragenter.prevent
      @dragover.prevent
    >
      <div
        v-for="item in getList(1)"
        :key="item.id"
        class="drag-el p-2.5 mb-2.5 last:mb-0 rounded bg-teal-600 cursor-grab text-white"
        draggable="true"
        @dragstart="startDrag($event, item)"
      >
        <template v-if="item.type === 'text'">
          {{ item.title }}
        </template>
        <template v-else-if="item.type === 'image'">
          <img :src="item.src" alt="Image" class="w-7 h-auto" @error="handleImageError" />
        </template>
      </div>
      </div>
    </div>
    <div class="w-6/12 mt-24">
    <h1 class="text-center font-bold text-3xl">Box 2</h1>
    <div
      class="drop-zone p-7 mt-2.5 bg-teal-100 rounded-md min-h-52"
      @drop.prevent="onDrop($event, 2)"
      @dragenter.prevent
      @dragover.prevent
    >
      <div
        v-for="item in getList(2)"
        :key="item.id"
        class="drag-el p-2.5 mb-2.5 last:mb-0 rounded bg-teal-600 cursor-grab text-white"
        draggable="true"
        @dragstart="startDrag($event, item)"
      >
        <template v-if="item.type === 'text'">
          {{ item.title }}
        </template>
        <template v-else-if="item.type === 'image'">
          <img :src="item.src" alt="Image" class="w-7 h-auto" @error="handleImageError" />
        </template>
      </div>
    </div>
  </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const items = ref([
      { id: 0, title: 'Item 1', list: 1, type: 'text' },
      { id: 1, title: 'Item 2', list: 1, type: 'text' },
      { id: 2, src: new URL('@/assets/logo.png', import.meta.url).href, list: 1, type: 'image' } 
    ])

    const getList = (list) => {
      return items.value.filter((item) => item.list === list)
    }

    const startDrag = (event, item) => {
      event.dataTransfer.effectAllowed = 'move'
      event.dataTransfer.setData('itemID', item.id)
    }

    const onDrop = (event, list) => {
      const itemID = event.dataTransfer.getData('itemID')
      const item = items.value.find((item) => item.id === parseInt(itemID))
      if (item) {
        // Remove item from its current list
        items.value = items.value.filter((i) => i.id !== item.id)

        // Calculate the drop index
        const dropZone = event.currentTarget
        const dropZoneRect = dropZone.getBoundingClientRect()
        const dropY = event.clientY - dropZoneRect.top

        const targetList = getList(list)
        let insertIndex = targetList.length

        for (let i = 0; i < targetList.length; i++) {
          const targetItem = dropZone.children[i]
          const targetRect = targetItem.getBoundingClientRect()
          if (dropY < targetRect.top + targetRect.height / 2) {
            insertIndex = i
            break
          }
        }

        // Insert item at the calculated position in the new list
        items.value.splice(insertIndex, 0, { ...item, list })
      }
    }

    const handleImageError = (event) => {
      event.target.src = new URL('@/assets/logo.png', import.meta.url).href // Provide a placeholder image path
    }

    return {
      getList,
      startDrag,
      onDrop,
      handleImageError
    }
  }
}
</script>

<style></style> 

