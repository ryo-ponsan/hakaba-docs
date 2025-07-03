# 実践演習課題

このファイルには、段階的にスキルアップできる実践的な課題を記載しています。

## 🟢 初級課題

### 課題1: Aboutページの作成

**目標:** ファイルベースルーティングとVue SFCの基礎を学ぶ

**手順:**

1. `pages/about.vue` を作成
2. 自己紹介コンテンツを追加
3. レイアウトでナビゲーションリンクを追加

**サンプルコード:**

```vue
<!-- pages/about.vue -->
<template>
  <div class="max-w-4xl mx-auto">
    <h1 class="text-4xl font-bold mb-6">About Me</h1>
    <div class="bg-white p-8 rounded-lg shadow-md">
      <p class="text-lg text-gray-700 mb-4">
        こんにちは！私はWebデベロッパーです。
      </p>
      <h2 class="text-2xl font-semibold mb-4">スキル</h2>
      <ul class="list-disc list-inside space-y-2">
        <li>Vue.js</li>
        <li>Nuxt.js</li>
        <li>JavaScript/TypeScript</li>
      </ul>
    </div>
  </div>
</template>
```

### 課題2: 投稿が0件の場合の表示

**目標:** 条件分岐（v-if）の理解

**修正箇所:** `pages/index.vue`

```vue
<template>
  <div>
    <h1 class="text-3xl font-bold mb-8">Blog Posts</h1>
    <div v-if="pending">Loading...</div>
    <div v-else-if="error">Error loading posts.</div>
    <div v-else-if="posts && posts.length === 0" class="text-center py-12">
      <h2 class="text-xl text-gray-600 mb-4">まだ投稿がありません</h2>
      <p class="text-gray-500">最初の投稿を作成してみましょう！</p>
    </div>
    <div v-else class="space-y-8">
      <!-- 既存の投稿一覧 -->
    </div>
  </div>
</template>
```

## 🟡 中級課題

### 課題3: 投稿カードコンポーネントの作成

**目標:** コンポーネント分割とpropsの理解

**手順:**

1. `components/Blog/PostCard.vue` を作成
2. `pages/index.vue` で使用

**サンプルコード:**

```vue
<!-- components/Blog/PostCard.vue -->
<template>
  <div
    class="bg-white p-6 rounded-lg shadow-md hover:shadow-lg transition-shadow duration-300"
  >
    <NuxtLink :to="post._path" class="block">
      <h2 class="text-2xl font-bold text-blue-700 hover:underline">
        {{ post.title }}
      </h2>
      <p class="text-gray-500 mt-2">{{ formatDate(post.date) }}</p>
      <p class="text-gray-600 mt-4">{{ post.description }}</p>
      <div v-if="post.tags" class="flex flex-wrap gap-2 mt-4">
        <span
          v-for="tag in post.tags"
          :key="tag"
          class="px-2 py-1 bg-blue-100 text-blue-800 rounded-full text-sm"
        >
          {{ tag }}
        </span>
      </div>
    </NuxtLink>
  </div>
</template>

<script setup>
defineProps({
  post: {
    type: Object,
    required: true,
  },
});

const formatDate = (date) => {
  return new Date(date).toLocaleDateString("ja-JP");
};
</script>
```

### 課題4: 検索機能の実装

**目標:** リアクティブデータとcomputed propertyの理解

**修正箇所:** `pages/index.vue`

```vue
<template>
  <div>
    <h1 class="text-3xl font-bold mb-8">Blog Posts</h1>

    <!-- 検索フィールド -->
    <div class="mb-6">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="投稿を検索..."
        class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
      />
    </div>

    <div v-if="pending">Loading...</div>
    <div v-else-if="error">Error loading posts.</div>
    <div v-else class="space-y-8">
      <PostCard v-for="post in filteredPosts" :key="post._path" :post="post" />
    </div>
  </div>
</template>

<script setup>
const searchQuery = ref("");

const {
  data: posts,
  pending,
  error,
} = await useAsyncData("posts", () =>
  queryContent("/articles").sort({ date: -1 }).find()
);

const filteredPosts = computed(() => {
  if (!searchQuery.value || !posts.value) return posts.value;

  return posts.value.filter(
    (post) =>
      post.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      post.description.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});
</script>
```

### 課題5: タグ機能の追加

**目標:** Markdownファイルのfrontmatterとデータ処理

**手順:**

1. Markdownファイルにタグを追加
2. タグフィルタリング機能を実装

**サンプル:** `content/articles/first-post.md`

```markdown
---
title: "My First Blog Post"
date: "2025-06-28"
description: "This is the very first post on my new blog. Welcome!"
tags: ["vue", "nuxt", "beginner"]
---
```

## 🔴 上級課題

### 課題6: 状態管理（Pinia）の導入

**目標:** グローバル状態管理の理解

**手順:**

1. Piniaをインストール: `npm install pinia @pinia/nuxt`
2. `nuxt.config.ts` にモジュール追加
3. ストアを作成

**サンプルコード:**

```javascript
// stores/blog.js
export const useBlogStore = defineStore("blog", () => {
  const posts = ref([]);
  const loading = ref(false);
  const searchQuery = ref("");
  const selectedTags = ref([]);

  const filteredPosts = computed(() => {
    let filtered = posts.value;

    // 検索クエリでフィルタ
    if (searchQuery.value) {
      filtered = filtered.filter((post) =>
        post.title.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    }

    // タグでフィルタ
    if (selectedTags.value.length > 0) {
      filtered = filtered.filter((post) =>
        post.tags?.some((tag) => selectedTags.value.includes(tag))
      );
    }

    return filtered;
  });

  const fetchPosts = async () => {
    loading.value = true;
    try {
      const { data } = await $fetch("/api/posts");
      posts.value = data;
    } finally {
      loading.value = false;
    }
  };

  return {
    posts,
    loading,
    searchQuery,
    selectedTags,
    filteredPosts,
    fetchPosts,
  };
});
```

### 課題7: Server API の実装

**目標:** Nuxt Server APIとデータベース連携

**サンプルコード:**

```javascript
// server/api/posts.get.js
export default defineEventHandler(async (event) => {
  try {
    // クエリパラメータの取得
    const query = getQuery(event);
    const { search, tag, page = 1, limit = 10 } = query;

    // Contentから投稿を取得
    let posts = await serverQueryContent("/articles").sort({ date: -1 }).find();

    // フィルタリング
    if (search) {
      posts = posts.filter((post) =>
        post.title.toLowerCase().includes(search.toLowerCase())
      );
    }

    if (tag) {
      posts = posts.filter((post) => post.tags?.includes(tag));
    }

    // ページネーション
    const total = posts.length;
    const offset = (page - 1) * limit;
    const paginatedPosts = posts.slice(offset, offset + limit);

    return {
      posts: paginatedPosts,
      pagination: {
        page: parseInt(page),
        limit: parseInt(limit),
        total,
        totalPages: Math.ceil(total / limit),
      },
    };
  } catch (error) {
    throw createError({
      statusCode: 500,
      statusMessage: "Failed to fetch posts",
    });
  }
});
```

### 課題8: 認証システムの実装

**目標:** ミドルウェアとセッション管理

**サンプルコード:**

```javascript
// middleware/auth.js
export default defineNuxtRouteMiddleware((to, from) => {
  const { $auth } = useNuxtApp();

  if (!$auth.user && to.path.startsWith("/admin")) {
    return navigateTo("/login");
  }
});
```

```vue
<!-- pages/login.vue -->
<template>
  <div class="max-w-md mx-auto mt-12">
    <form @submit.prevent="login" class="bg-white p-8 rounded-lg shadow-md">
      <h1 class="text-2xl font-bold mb-6">ログイン</h1>

      <div class="mb-4">
        <label class="block text-sm font-medium mb-2">メールアドレス</label>
        <input
          v-model="form.email"
          type="email"
          required
          class="w-full p-3 border rounded-lg focus:ring-2 focus:ring-blue-500"
        />
      </div>

      <div class="mb-6">
        <label class="block text-sm font-medium mb-2">パスワード</label>
        <input
          v-model="form.password"
          type="password"
          required
          class="w-full p-3 border rounded-lg focus:ring-2 focus:ring-blue-500"
        />
      </div>

      <button
        type="submit"
        :disabled="loading"
        class="w-full bg-blue-600 text-white py-3 rounded-lg hover:bg-blue-700 disabled:opacity-50"
      >
        {{ loading ? "ログイン中..." : "ログイン" }}
      </button>
    </form>
  </div>
</template>

<script setup>
const form = reactive({
  email: "",
  password: "",
});

const loading = ref(false);

const login = async () => {
  loading.value = true;
  try {
    await $fetch("/api/auth/login", {
      method: "POST",
      body: form,
    });
    await navigateTo("/admin");
  } catch (error) {
    console.error("Login failed:", error);
  } finally {
    loading.value = false;
  }
};
</script>
```

## 📝 追加学習課題

### UI/UX改善

1. ダークモード対応
2. レスポンシブデザインの最適化
3. アニメーション効果の追加
4. アクセシビリティの向上

### パフォーマンス最適化

1. 画像の遅延読み込み
2. 無限スクロール
3. Service Worker でのキャッシュ
4. バンドルサイズの最適化

### テスト実装

1. ユニットテスト（Vitest）
2. コンポーネントテスト（Vue Test Utils）
3. E2Eテスト（Playwright）
4. ビジュアルリグレッションテスト

これらの課題を順番に取り組むことで、実践的なVue.js/Nuxt.jsスキルを身につけることができます。
