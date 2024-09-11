<script setup>
const toast = useToast()
</script>

<template>
  <div class="h-full flex items-center justify-center">
    <UCard class="w-full max-w-md">
      <template #header>
        <h1 class="text-2xl font-bold text-center">
          Neural Nest
        </h1>
      </template>
      <p class="text-center">
        Neural Nest is a platform with some tools to help you with your meditation practice.
        Idk, it's still in the early stages and I'm working on it purely out of boredom.
        I'll probably add some more tools and resources over time.
      </p>
      <template #footer>
        <UButton label="Show toast" @click="toast.add({ title: 'Hello world!' })" />
      </template>
    </UCard>
  </div>
</template>
