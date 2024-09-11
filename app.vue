<template>
  <div class="h-svh w-screen flex flex-col">
    <!-- Top bar -->
    <header class="bg-gray-800 text-white p-4">
      <h1 class="text-xl font-bold text-center">
        Self Improvement App
      </h1>
    </header>

    <!-- Main content area -->
    <main class="flex-grow overflow-auto">
      <NuxtPage />
    </main>

    <!-- Bottom bar -->
    <footer class="bg-gray-800 text-white p-4 flex justify-between items-center">
      <div class="">
        <UButton to="/">
          Home
        </UButton>
        <UButton to="/wimhof">
          Wimhof
        </UButton>
      </div>
      <span>© 2024 Floo App</span>
    </footer>

    <!-- Toast notifications -->
    <UNotifications />
  </div>
</template>
