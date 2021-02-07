---
layout: post
title: 'Ruby on Rails - content_for 外框佈局'
date: 2018-12-11 08:49
comments: true
categories:
- Ruby on Rails
---
## content_for

content_for 是一個外部框層的 view 渲染方法，
傳統上，我們會使用 yield 做 “內容” 的渲染，
像是:
```html
<body>
<%= yield %>
</body>
```
yield 會代表其他頁面的 html 內容。

而 content_for 則是幫我們決定外框要套上哪些 html 內容，
但需要搭配有命名過的 yield 。
例如:
```html
<nav>
	<%= yeild :mobile %>
</nav>
```
```html
<% content_for :mobile do %>
	<li>首頁</li>
  <li>手機版會員中心</li>
  <li>手機版商城</li>
<% end %>
```
會合併產生:
```
<nav>
	<li>首頁</li>
  <li>手機版會員中心</li>
  <li>手機版商城</li>
</nav>
```
這是一個把 html 程式碼更物件化的概念，
很適合用在具有多種變化但不想重用 html ，且無引入 vue 等 js framework 的 Rails 專案。

