<script setup lang="ts">
import { ref, computed } from 'vue'

const props = defineProps<{
  es: number
}>()

const count = ref<number>(0)
const countMessage = computed(() => '回数: ' + count.value)

function increment():string {
  count.value += props.es;
  return countMessage.value
}
</script>

<template>
  <div>
    <div>増加量: {{ es }}</div>
    <div>{{ countMessage }}</div>
    <button @click="increment()">クリック</button>
    <button @click="count = 0">リセット</button>
    <button @click="count -= props.es">減少</button>
  </div>
</template>

<style></style>