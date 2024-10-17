---
title: VueRouter的两种模式
categories:
  - 前端开发
tags:
  - VueRouter
abbrlink: bb1a58b6
date: 2024-04-25 00:00:00
cover:
---

Vue Router 提供了两种主要的路由模式：**hash 模式**和 **history 模式**。它们的主要区别在于浏览器地址栏 URL 的处理方式，影响路由实现的原理以及页面的加载和刷新行为。下面详细说明它们的区别、优缺点以及实现原理。

# hash 模式

## 实现原理：

- **hash 模式**使用的是 URL 的哈希值（`#`号后面的部分）来模拟不同的路径，而无需向服务器发出请求。
- 哈希值在浏览器中被称为“片段标识符”（Fragment Identifier），用于指定页面中的某个位置。它的一个特性是：改变哈希部分不会触发页面的重新加载。

## URL 示例：

```
http://example.com/#/home
```

这里的 `#/home` 是哈希部分，浏览器只会处理 `#` 之前的部分，也就是 `http://example.com`，不会向服务器发送 `/home` 请求。

## 优点：

1. **简单易用**：不用配置服务器，默认情况下，hash 模式能兼容所有浏览器，适合开发阶段或对 SEO 要求不高的项目。
2. **不会导致页面刷新**：改变哈希值时，浏览器不会发出 HTTP 请求，不会触发页面重新加载。
3. **兼容性强**：由于所有现代浏览器都支持哈希值，该模式在所有浏览器中表现一致。

## 缺点：

1. **SEO 不友好**：哈希符号（`#`）之后的内容不会被搜索引擎索引，因此不利于搜索引擎优化（SEO）。虽然一些搜索引擎可以处理哈希路由，但效果不佳。
2. **不美观**：URL 中带有 `#`，相对而言没有 `history` 模式下的 URL 直观、简洁。

## 实现原理细节：

- Vue Router 通过监听 `window.onhashchange` 事件，当 URL 哈希值变化时，更新视图以匹配当前路由。
- 由于哈希变化不触发页面刷新，Vue Router 可以根据不同的哈希值切换组件，实现单页应用（SPA）的路由管理。

------

# history 模式

## 实现原理：

- **history 模式**使用 HTML5 提供的 `history.pushState` 和 `history.replaceState` API 来进行 URL 路由管理。这些 API 允许在浏览器地址栏中改变路径，而不触发页面刷新。
- 通过 `pushState`，可以改变地址栏中的 URL，同时保持页面不重新加载，也可以通过前进和后退按钮来切换历史记录。

## URL 示例：

```
http://example.com/home
```

在 history 模式下，`/home` 是一个真正的路径，不包含 `#`，看起来像标准的 URL 路径。

## 优点：

1. **SEO 友好**：由于 history 模式使用的是标准的 URL，搜索引擎可以正常抓取这些页面，因此对 SEO 更加友好。
2. **URL 更加美观**：没有 `#`，URL 更加简洁直观，与传统的多页面应用无异。
3. **前进/后退更自然**：使用浏览器的历史记录功能时，用户体验更加自然流畅。

## 缺点：

1. **需要服务器支持**：由于 history 模式的 URL 是真实的路径，浏览器刷新时会向服务器发出请求。如果服务器没有适当的配置，刷新时会出现 404 错误。因此，需要在服务器上配置一个所有路径都指向应用的路由入口文件（通常是 `index.html`），例如在 Nginx 或 Apache 上做相应配置。
2. **兼容性问题**：history API 在老旧的浏览器中可能不被完全支持，尽管绝大多数现代浏览器都支持了。

## 实现原理细节：

- Vue Router 在 history 模式下通过 `history.pushState` 和 `history.replaceState` 来更改地址栏 URL，而不触发页面刷新。
- 当用户点击浏览器的前进或后退按钮时，浏览器会触发 `popstate` 事件，Vue Router 会监听此事件并匹配相应的路由组件。

## 服务器配置示例：

- **Nginx** 配置：

  ```
  nginx复制代码location / {
    try_files $uri $uri/ /index.html;
  }
  ```

- **Apache** 配置：

  ```
  apache复制代码<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteRule ^index\.html$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.html [L]
  </IfModule>
  ```

------

# 两者的对比总结

| 特性         | Hash 模式                      | History 模式                           |
| ------------ | ------------------------------ | -------------------------------------- |
| URL 形式     | `http://example.com/#/home`    | `http://example.com/home`              |
| 页面刷新     | 改变 hash 不刷新页面           | 改变 URL 不刷新页面                    |
| SEO 友好性   | 差，哈希部分不会被搜索引擎索引 | 好，标准 URL 可以被索引                |
| 浏览器兼容性 | 兼容性好，支持所有浏览器       | 需要现代浏览器支持 history API         |
| 服务器支持   | 不需要额外配置服务器           | 需要服务器配置，将所有路径指向入口文件 |
| URL 美观     | 不美观，带有 `#`               | 更美观，URL 简洁                       |
| 实现复杂度   | 简单，利用 `onhashchange`      | 需要监听 `popstate` 事件，复杂度稍高   |

------

# 总结与建议

- **hash 模式**：适合简单应用或开发阶段，尤其是在不方便配置服务器或者不需要考虑 SEO 的情况下。
- **history 模式**：更适合生产环境，特别是需要考虑 SEO 的应用，需要美观的 URL 和更好的用户体验时推荐使用，但要确保服务器配置正确。