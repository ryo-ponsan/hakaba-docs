# Nuxt.js Blog - Learning Document

このドキュメントでは、プロジェクトの構造、使用技術、ブログコンテンツの管理方法について説明します。Nuxt.jsやモダンなWeb技術の学習を支援することを目的としています。

## 📚 詳細な学習ガイド

より体系的な学習を進めたい場合は、以下のドキュメントをご参照ください：

- **[LEARNING_GUIDE.md](./LEARNING_GUIDE.md)** - Vue.js & Nuxt.js の包括的な学習ガイド
- **[EXERCISES.md](./EXERCISES.md)** - 段階的な実践演習課題

これらのガイドでは、初級から上級まで段階的にスキルアップできる内容を提供しています。

## Technologies Used

- **[Nuxt.js](https://nuxt.com/)**: The core framework. It's a meta-framework for Vue.js that provides structure and conventions, making it easier to build server-rendered applications.
- **[Vue.js](https://vuejs.org/)**: The underlying JavaScript framework that Nuxt.js is built upon.
- **[@nuxt/content](https://content.nuxt.com/)**: A Nuxt module that turns your project into a Git-based Headless CMS. It reads Markdown files from the `content/` directory and makes them available for you to display as pages.
- **[@nuxtjs/tailwindcss](https://tailwindcss.nuxtjs.org/)**: A Nuxt module for integrating [Tailwind CSS](https://tailwindcss.com/), a utility-first CSS framework for rapidly building custom designs.
- **[@tailwindcss/typography](https://tailwindcss.com/docs/typography-plugin)**: A plugin for Tailwind CSS that provides beautiful typographic defaults for prose content, like our blog posts.
- **[nuxt-icon](https://github.com/nuxt-modules/icon)**: A Nuxt module for easily adding and using icons from various popular icon sets.

## Project Structure

```
.
├── .nuxt/              # Nuxt's build directory (auto-generated)
├── components/         # Vue components (not used in this simple setup yet)
├── content/
│   └── articles/       # Your blog posts (Markdown files) go here
│       ├── first-post.md
│       └── second-post.md
├── layouts/
│   └── default.vue     # The main layout for the site (header, footer, etc.)
├── node_modules/       # Project dependencies
├── pages/
│   ├── index.vue       # The homepage (displays a list of posts)
│   └── articles/
│       └── [...slug].vue # A dynamic page to display a single blog post
├── public/             # Static assets (favicon, etc.)
├── app.vue             # The main application component
├── nuxt.config.ts      # Nuxt configuration file
├── package.json        # Project dependencies and scripts
├── tailwind.config.js  # Tailwind CSS configuration
└── LEARNING.md         # This file
```

## How It Works

### 1. File-based Routing

Nuxt automatically creates URL routes based on the file structure inside the `pages/` directory.

- `pages/index.vue` maps to the `/` route (the homepage).
- `pages/articles/[...slug].vue` is a special dynamic route. It will match any URL that starts with `/articles/`, like `/articles/first-post` or `/articles/another-post`. The `[...slug]` part captures the rest of the URL path.

### 2. Content Management

- The `@nuxt/content` module reads all the `.md` files from the `content/articles/` directory.
- In `pages/index.vue`, we use `queryContent('/articles').find()` to get a list of all posts.
- In `pages/articles/[...slug].vue`, the `<ContentDoc />` component automatically fetches and renders the Markdown file that corresponds to the current URL.

### 3. Layouts

The `layouts/default.vue` file defines the overall page structure. The `<slot />` component inside it is a placeholder where the content of the current page (from the `pages/` directory) will be injected.

## Managing Blog Posts

### Adding a New Post

1.  Create a new `.md` file inside the `content/articles/` directory (e.g., `my-new-awesome-post.md`).
2.  The filename will become the URL slug (e.g., `/articles/my-new-awesome-post`).
3.  Add "front-matter" to the top of the file. This is YAML metadata used by Nuxt Content.

    ```yaml
    ---
    title: "Title of Your Post"
    date: "YYYY-MM-DD"
    description: "A short summary of your post that will appear in the post list."
    ---
    ```

4.  Write your post content below the front-matter using Markdown.

### Editing a Post

- Simply open the corresponding `.md` file in the `content/articles/` directory and edit it.

## Running the Project

1.  **Install dependencies:**
    ```bash
    npm install
    ```
2.  **Start the development server:**
    ```bash
    npm run dev
    ```
3.  Open your browser to `http://localhost:3000`.

## Future Customization Ideas

- **Add Components:** Create reusable Vue components in the `components/` directory (e.g., a custom button, an author bio card).
- **Style More:** Customize the design further by editing `tailwind.config.js` and using Tailwind's utility classes.
- **Add Tags:** Add a `tags` array to the front-matter of your posts and create pages to display posts by tag.
- **Pagination:** If you have many posts, implement pagination on the homepage.
- **Dark Mode:** Use Tailwind's dark mode feature to add a dark theme to your blog.
