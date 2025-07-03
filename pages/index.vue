<template>
  <div>
    <h1 class="text-3xl font-bold mb-8">Blog Posts</h1>
    <div v-if="pending" class="text-center py-8">
      <p class="text-gray-600">Loading...</p>
    </div>
    <div
      v-else-if="error"
      class="bg-red-50 border border-red-200 rounded-lg p-6"
    >
      <h2 class="text-red-800 text-lg font-semibold mb-2">
        Error loading posts
      </h2>
      <pre class="text-red-600 text-sm">{{ error }}</pre>
    </div>
    <div v-else-if="!posts || posts.length === 0" class="text-center py-12">
      <h2 class="text-xl text-gray-600 mb-4">まだ投稿がありません</h2>
      <p class="text-gray-500">最初の投稿を作成してみましょう！</p>
    </div>
    <div v-else class="space-y-8">
      <div
        v-for="post in posts"
        :key="post._path"
        class="bg-white p-6 rounded-lg shadow-md hover:shadow-lg transition-shadow duration-300"
      >
        <NuxtLink :to="post._path" class="block">
          <h2 class="text-2xl font-bold text-blue-700 hover:underline">
            {{ post.title }}
          </h2>
          <p class="text-gray-500 mt-2">
            {{ new Date(post.date).toLocaleDateString() }}
          </p>
          <p class="text-gray-600 mt-4">{{ post.description }}</p>
        </NuxtLink>
      </div>
    </div>
  </div>
</template>

<script setup>
// 最も直接的な方法: useAsyncDataを使わずに直接取得
try {
  // queryContentが自動インポートされているかテスト
  const posts = await queryContent("articles").sort({ date: -1 }).find();
  console.log("Direct queryContent success:", posts);
} catch (directError) {
  console.error("Direct queryContent failed:", directError);
}

// useAsyncDataでラップ
const {
  data: posts,
  pending,
  error,
} = await useAsyncData("posts", async () => {
  // 複数の方法を試す
  const methods = [
    // 方法1: 標準的なqueryContent
    () => queryContent("articles").sort({ date: -1 }).find(),
    // 方法2: パスを明示
    () => queryContent("/articles").sort({ date: -1 }).find(),
    // 方法3: whereを使用
    () =>
      queryContent()
        .where({ _path: { $regex: "^/articles" } })
        .sort({ date: -1 })
        .find(),
  ];

  for (const [index, method] of methods.entries()) {
    try {
      console.log(`Trying method ${index + 1}`);
      const result = await method();
      console.log(`Method ${index + 1} success:`, result);
      if (result && result.length > 0) {
        return result;
      }
    } catch (err) {
      console.error(`Method ${index + 1} failed:`, err);
    }
  }

  console.log("All methods failed, returning empty array");
  return [];
});
</script>
