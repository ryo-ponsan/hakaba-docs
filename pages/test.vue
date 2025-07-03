<template>
  <div class="p-8">
    <h1 class="text-2xl font-bold mb-4">Content Test Page</h1>

    <div v-if="pending">Loading...</div>
    <div v-else-if="error" class="bg-red-100 p-4 rounded">
      <h2 class="text-red-800 font-bold">Error:</h2>
      <pre class="text-red-600">{{ error }}</pre>
    </div>
    <div v-else>
      <h2 class="text-lg font-semibold mb-2">
        Found {{ posts?.length || 0 }} articles:
      </h2>
      <ul class="list-disc list-inside">
        <li v-for="post in posts" :key="post._path" class="mb-2">
          <strong>{{ post.title }}</strong> - {{ post.date }}
          <br />
          <small class="text-gray-600">Path: {{ post._path }}</small>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
// テスト用のシンプルなクエリ
const {
  data: posts,
  pending,
  error,
} = await useAsyncData("test-posts", () => queryContent("/articles").find());

// デバッグ情報をコンソールに出力
console.log("Posts:", posts.value);
console.log("Error:", error.value);
</script>
