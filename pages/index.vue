<script setup>
const toast = useToast()
</script>

<template>
  <div class="h-full flex items-center justify-center">
    <UCard class="w-full max-w-md">
      <template #header>
        <h1 class="text-2xl font-bold text-center">
          Welcome to My Nuxt 3 App
        </h1>
      </template>
      <p class="text-center">
        This is a basic example using Tailwind CSS and Nuxt UI.
      </p>
      <template #footer>
        <UButton label="Show toast" @click="toast.add({ title: 'Hello world!' })" />
      </template>
    </UCard>
  </div>
</template>
