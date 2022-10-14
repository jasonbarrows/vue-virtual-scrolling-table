<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue';

const data = Array(250).fill().map((e1, i) => {
  return { id: i + 1, data: Array(20).fill().map((e2, j) => 'r' + (i + 1) + 'c' + (j + 1)) }
})
const rowHeight = 40
const nodePadding = 0
const windowHeight = ref(window.innerHeight)
const scrollTop = ref(0)
const heading = ref(null)

const rootHeight = computed(() => windowHeight.value - 40)
const dataHeight = data.length * rowHeight
const startIndex = computed(() => {
    let startNode = Math.floor(scrollTop.value / rowHeight) - nodePadding
    startNode = Math.max(0, startNode)
    return startNode
})
const visibleNodeCount = computed(() => {
    let count = Math.ceil(rootHeight.value / rowHeight) + nodePadding * 2;
    count = Math.min(data.length - startIndex.value, count);
    return count + 1
})
const visibleItems = computed(() => data.slice(startIndex.value, startIndex.value + visibleNodeCount.value))
const offsetY = computed(() => startIndex.value * rowHeight)
const handleScroll = e => {
    requestAnimationFrame(() => {
        scrollTop.value = e.target.scrollTop
    })
}
const linkUpHeaders = e => {
  requestAnimationFrame(() => {
    heading.value.scrollLeft = e.target.scrollLeft
  })
}
const handleResize = e => windowHeight.value = e.target.innerHeight
onMounted(() => window.addEventListener('resize', handleResize))
onUnmounted(() => window.removeEventListener('resize', handleResize))

</script>

<template>
  <div ref="heading" class="overflow-hidden">
    <table class="table-fixed border-separate border-spacing-0">
      <thead>
        <tr class="h-10">
          <th class="min-w-[112px] max-w-[112px] bg-white border-b-2 border-r border-gray-400 sticky left-0"></th>
          <th v-for="i in data[0].data.length"
            class="min-w-[96px] max-w-[96px] border-t border-b-2 border-r border-gray-400">
            Column {{ i }}
          </th>
        </tr>
      </thead>
    </table>
  </div>
  <div class="overflow-auto" :style="{height: rootHeight + 'px'}" @scroll="handleScroll">
    <div class="overflow-x-auto" :style="{height: dataHeight + 'px'}" @scroll="linkUpHeaders">
      <table class="table-fixed border-separate border-spacing-0" :style="{ transform: 'translate(0, ' + offsetY + 'px)' }">
        <tbody :style="{ height: rootHeight + 'px' }">
          <tr class="h-10" v-for="(row, i) in visibleItems" :key="i">
            <td class="min-w-[112px] max-w-[112px] w-28 font-bold bg-white border-l border-r border-b border-gray-400 sticky left-0">
                Row {{ row.id }}
            </td>
            <td v-for="item in row.data"
              class="min-w-[96px] max-w-[96px] w-24 px-1 text-right border-r border-b border-gray-400">
              {{ item }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
