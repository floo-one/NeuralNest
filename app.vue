<template>
  <div class="h-svh w-screen flex flex-col">
    <!-- Top bar -->
    <header class="bg-blue-950 text-white p-4">
      <h1 class="text-xl font-bold text-center">
        Neural Nest
      </h1>
    </header>

    <!-- Main content area -->
    <main class="flex-grow overflow-auto">
      <NuxtPage />
    </main>

    <!-- Bottom bar -->
    <footer class="bg-gray-800 text-white p-4 flex justify-between items-center">
      <div class="">
        <UButton to="/" color="white" variant="ghost">
          Home
        </UButton>
        <UButton to="/wimhof" color="white" variant="ghost">
          Wimhof
        </UButton>
      </div>

      <span>
        <NuxtLink to="https://floo.one" target="_blank">
          © 2024 Neural Nest
          <UBadge color="gray" variant="solid">by floo.one</UBadge>
        </NuxtLink>
      </span>
    </footer>
  </div>
  <UNotifications />
</template>
