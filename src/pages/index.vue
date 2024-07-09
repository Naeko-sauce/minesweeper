<script setup lang="ts">

</script>

<template>
  <div>
    Minsweeper

    <div
      v-for="y in 10"
      :key="y"
    >
      <button
        v-for="i in 10"
        :key="i"
        h-10 w-10 border
      >
        {{ (y - 1) * 10 + i }}
      </button>
    </div>
  </div>
</template>
