# sprout-cdn

静态资源 CDN，基于 Cloudflare Pages 部署，供其它网页跨域调用。

线上地址：<https://sprout-cdn.pages.dev>

## 特性

- 🌐 **全站 CORS**：所有资源开启 `Access-Control-Allow-Origin: *`，任意网页可直接引用
- ⚡ **长缓存**：按资源类型配置缓存策略，回源少、加载快
- 🚀 **全球加速**：Cloudflare Pages 边缘网络分发
- 🔤 **字体托管**：内置 LXGW WenKai Mono、Maple Mono 等中文字体

## 目录结构

```
sprout-cdn/
├── fonts/    # 字体文件（woff2）
├── css/      # 样式文件
├── js/       # 脚本文件
├── img/      # 图片资源（强缓存 immutable）
├── json/     # JSON 数据（no-cache，保证实时性）
├── files/    # 其它静态文件
├── _headers  # Cloudflare Pages 自定义响应头（CORS + 缓存策略）
└── index.html# 资源目录页
```

## 缓存策略

| 路径 | 策略 |
|------|------|
| `/fonts/*`、`/css/*`、`/js/*`、`/files/*` | `max-age=2592000`（30 天）+ `stale-while-revalidate=604800` + `stale-if-error=604800` |
| `/img/*` | `max-age=31536000, immutable`（1 年） |
| `/json/*` | `max-age=60` + `stale-while-revalidate=600`（可被边缘缓存，最多 1 分钟旧数据） |

> 文件名未做 hash 版本化，因此非图片资源统一用 30 天而非 1 年 `immutable`，避免更新后客户端长期不刷新。
> 若后续改为 `app.3f2a1c.css` 这类带 hash 的文件名，可把该目录改成 `max-age=31536000, immutable`。

## 使用示例

### 引用字体

```css
@font-face {
  font-family: 'LXGWWenKaiMono';
  src: url('https://sprout-cdn.pages.dev/fonts/LXGWWenKaiMono-Regular.woff2') format('woff2');
  font-weight: 400;
}
body { font-family: 'LXGWWenKaiMono', monospace; }
```

### 引用其它资源

```html
<link rel="stylesheet" href="https://sprout-cdn.pages.dev/css/example.css">
<script src="https://sprout-cdn.pages.dev/js/example.js"></script>
<img src="https://sprout-cdn.pages.dev/img/example.svg" alt="">
```

```js
fetch('https://sprout-cdn.pages.dev/json/example.json')
  .then(res => res.json())
  .then(console.log);
```

## 部署

本目录以 Direct Upload 方式部署（未绑定 Git 自动构建）：

```bash
npx wrangler pages deploy . --project-name sprout-cdn --branch main
```

本地预览：

```bash
npx wrangler pages dev .
```

## License

仅供个人项目使用的资源托管仓库。
