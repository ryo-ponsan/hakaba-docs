<template>
  <main>
    <ContentDoc v-slot="{ doc }">
      <article class="prose lg:prose-xl max-w-none bg-white p-8 rounded-lg shadow-md">
        <h1 class="text-4xl font-bold mb-2">{{ doc.title }}</h1>
        <p class="text-gray-500 text-lg mb-8">{{ new Date(doc.date).toLocaleDateString() }}</p>
        <ContentRenderer :value="doc" />
      </article>
    </ContentDoc>
  </main>
</template>

<style>
.prose h1, .prose h2, .prose h3, .prose h4, .prose h5, .prose h6 {
  color: #2c5282; /* text-blue-800 */
}

.prose a {
  color: #3182ce; /* text-blue-600 */
}

.prose a:hover {
  text-decoration: underline;
}
</style>
