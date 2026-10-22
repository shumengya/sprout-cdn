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
| `/fonts/*`、`/css/*` | `max-age=604800`（7 天）+ `stale-while-revalidate` |
| `/img/*` | `max-age=31536000, immutable`（1 年） |
| `/json/*` | `no-cache`（每次校验） |

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

推送到 `main` 分支即自动触发 Cloudflare Pages 部署（已在 Cloudflare Pages 控制台绑定本仓库）。

本地预览：

```bash
npx wrangler pages dev .
```

## License

仅供个人项目使用的资源托管仓库。
