<template>
  <div class="min-h-screen bg-gray-50 text-gray-800">
    <header class="bg-white shadow-sm">
      <nav class="container mx-auto px-4 py-4 flex justify-between items-center">
        <NuxtLink to="/" class="text-2xl font-bold text-blue-600">My Blog</NuxtLink>
        <ul class="flex space-x-4">
          <li><NuxtLink to="/" class="text-gray-600 hover:text-blue-500">Home</NuxtLink></li>
        </ul>
      </nav>
    </header>

    <main class="container mx-auto px-4 py-8">
      <slot />
    </main>

    <footer class="bg-white mt-12 py-6">
      <div class="container mx-auto px-4 text-center text-gray-500">
        <p>&copy; 2025 My Blog. All rights reserved.</p>
      </div>
    </footer>
  </div>
</template>

<style>
body {
  @apply bg-gray-50;
}
</style>
