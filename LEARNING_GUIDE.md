# Vue.js & Nuxt.js 学習ガイド

このプロジェクトは、Vue.js と Nuxt.js の学習を目的としたブログアプリケーションです。段階的に学習を進められるよう、各ファイルや機能で学べる内容を整理しました。

## 📚 学習レベル別ガイド

### 🟢 初級レベル（Vue.js基礎）

#### 1. Vue.jsの基本構文 - `layouts/default.vue`

**学習ポイント:**

- Vue Single File Component (SFC) の構造
- テンプレート構文 (`<template>`)
- クラスバインディング
- Nuxtコンポーネント (`<NuxtLink>`, `<slot>`)

**実践課題:**

```vue
<!-- 新しいナビゲーションリンクを追加 -->
<li><NuxtLink to="/about" class="text-gray-600 hover:text-blue-500">About</NuxtLink></li>
```

#### 2. 条件分岐とリスト表示 - `pages/index.vue`

**学習ポイント:**

- `v-if`, `v-else-if`, `v-else` ディレクティブ
- `v-for` ディレクティブとキーバインディング
- ローディング状態の管理
- エラーハンドリング

**実践課題:**

- 投稿が0件の場合の表示を追加
- 投稿にタグ機能を追加してフィルタリング

### 🟡 中級レベル（Nuxt.js機能）

#### 3. ファイルベースルーティング

**ファイル構造:**

```
pages/
├── index.vue           # / ルート
├── articles/
│   └── [...slug].vue   # /articles/* 動的ルート
```

**学習ポイント:**

- 自動ルーティング生成
- 動的ルートパラメータ (`[...slug]`)
- ネストしたルート構造

**実践課題:**

- `/about.vue` ページを作成
- `/categories/[category].vue` で カテゴリ別表示

#### 4. Composition API - `pages/index.vue`

**学習ポイント:**

```javascript
const {
  data: posts,
  pending,
  error,
} = await useAsyncData("posts", () =>
  queryContent("/articles").sort({ date: -1 }).find()
);
```

- `useAsyncData` でのデータフェッチ
- 非同期処理の状態管理
- リアクティブデータ

**実践課題:**

- ページネーション機能の実装
- 検索機能の追加

#### 5. Content Module - Markdownベースのコンテンツ管理

**学習ポイント:**

- `@nuxt/content` モジュールの使用
- Frontmatter（メタデータ）の活用
- `queryContent()` API
- `<ContentDoc>` と `<ContentRenderer>` コンポーネント

**実践課題:**

- 新しいMarkdownファイルの作成
- カスタムフィールド（author, tags）の追加

### 🔴 上級レベル（アーキテクチャ・最適化）

#### 6. CSS フレームワーク統合 - Tailwind CSS

**学習ポイント:**

- ユーティリティファーストCSS
- レスポンシブデザイン
- Tailwind設定のカスタマイズ
- `@tailwindcss/typography` プラグイン

**実践課題:**

- ダークモード対応
- カスタムカラーパレットの作成

#### 7. SEO最適化

**現在の実装:**

- SSR（Server-Side Rendering）
- 自動的なメタタグ生成

**学習・改良ポイント:**

- `useSeoMeta()` でのメタタグ管理
- Open Graph タグの実装
- サイトマップ生成

## 🛠️ 改良提案と学習ポイント

### 1. 状態管理の導入（Pinia）

**現在の課題:** グローバル状態がない
**改良案:**

```javascript
// stores/blog.js
export const useBlogStore = defineStore("blog", () => {
  const posts = ref([]);
  const categories = ref([]);

  const fetchPosts = async () => {
    // 投稿データの取得
  };

  return { posts, categories, fetchPosts };
});
```

**学習ポイント:** Vue 3 Composition API ベースの状態管理

### 2. コンポーネント設計の改善

**現在の課題:** 単一ファイルに機能が集中
**改良案:**

```
components/
├── Blog/
│   ├── PostCard.vue     # 投稿カード
│   ├── PostList.vue     # 投稿一覧
│   └── PostFilter.vue   # フィルタリング
├── UI/
│   ├── Button.vue       # 再利用可能ボタン
│   └── Modal.vue        # モーダル
```

**学習ポイント:** コンポーネント分割、props/emit、スロット

### 3. フォーム機能の追加

**改良案:**

```vue
<!-- pages/admin/create.vue -->
<template>
  <form @submit.prevent="submitPost">
    <input v-model="form.title" placeholder="タイトル" />
    <textarea v-model="form.content" placeholder="内容"></textarea>
    <button type="submit">投稿</button>
  </form>
</template>
```

**学習ポイント:** フォームバリデーション、`v-model`、イベントハンドリング

### 4. API統合

**改良案:**

```javascript
// server/api/posts.get.js
export default defineEventHandler(async (event) => {
  // データベースからの投稿取得
  return await getPosts();
});
```

**学習ポイント:** Nuxt Server API、RESTful設計

### 5. 認証システム

**改良案:**

```javascript
// middleware/auth.js
export default defineNuxtRouteMiddleware((to, from) => {
  const { $auth } = useNuxtApp();
  if (!$auth.user) {
    return navigateTo("/login");
  }
});
```

**学習ポイント:** ミドルウェア、認証フロー、セッション管理

### 6. テスト実装

**改良案:**

```javascript
// tests/components/PostCard.test.js
import { mount } from "@vue/test-utils";
import PostCard from "~/components/Blog/PostCard.vue";

describe("PostCard", () => {
  test("renders post title correctly", () => {
    const wrapper = mount(PostCard, {
      props: { post: { title: "Test Post" } },
    });
    expect(wrapper.text()).toContain("Test Post");
  });
});
```

**学習ポイント:** Vue Test Utils、ユニットテスト、E2Eテスト

### 7. パフォーマンス最適化

**改良案:**

- 画像最適化（`@nuxt/image`）
- 遅延読み込み
- バンドルサイズ最適化
- PWA対応

**学習ポイント:** Web Vitals、最適化手法

## 📋 学習ロードマップ

### Week 1-2: Vue.js基礎

1. SFC構造の理解
2. テンプレート構文の習得
3. ディレクティブの活用

### Week 3-4: Nuxt.js基礎

1. ファイルベースルーティング
2. レイアウトシステム
3. Content Moduleの活用

### Week 5-6: 中級機能

1. Composition API
2. 状態管理（Pinia）
3. コンポーネント設計

### Week 7-8: 実践的機能

1. フォーム処理
2. API統合
3. 認証システム

### Week 9-10: 上級トピック

1. テスト実装
2. パフォーマンス最適化
3. デプロイメント

## 🎯 実践プロジェクト提案

### 1. 個人ポートフォリオサイト

- プロジェクト紹介ページ
- スキル管理
- お問い合わせフォーム

### 2. Eコマースサイト

- 商品一覧・詳細
- ショッピングカート
- 決済システム統合

### 3. SNSアプリケーション

- ユーザー認証
- 投稿・コメント機能
- リアルタイム更新

## 📖 推奨リソース

### 公式ドキュメント

- [Vue.js 公式ガイド](https://ja.vuejs.org/)
- [Nuxt.js 公式ドキュメント](https://nuxt.com/)
- [Tailwind CSS ドキュメント](https://tailwindcss.com/)

### 学習サイト

- Vue School
- Vue Mastery
- Nuxt Academy

このプロジェクトを基盤として、段階的に機能を追加・改良していくことで、モダンなWebアプリケーション開発のスキルを体系的に学習できます。
