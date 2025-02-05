# Windsurf 从零开始：构建完整博客网站的详细教程

今天，我将带领你从零开始，使用 Windsurf 框架构建一个功能齐全的博客网站。无论你是编程新手还是经验丰富的开发者，这篇教程都将一步步引导你完成整个项目。我们的目标是让你在阅读完这篇教程后，能够独立使用 Windsurf 构建自己的 Web 应用程序。

## 目录

1. [项目介绍](#项目介绍)
2. [环境准备](#环境准备)
3. [创建 Windsurf 项目](#创建-windsurf-项目)
4. [项目结构](#项目结构)
5. [构建博客首页](#构建博客首页)
6. [用户注册与登录](#用户注册与登录)
7. [创建博客文章](#创建博客文章)
8. [查看博客文章](#查看博客文章)
9. [编辑与删除博客文章](#编辑与删除博客文章)
10. [部署项目](#部署项目)
11. [总结](#总结)

## 项目介绍

我们将构建一个简单的博客网站，用户可以注册、登录，创建、查看、编辑和删除博客文章。这个项目将涵盖 Windsurf 的核心功能，包括路由、中间件、模板引擎、数据库集成等。

## 环境准备

在开始之前，请确保你的开发环境已经配置好：

- **Node.js**：从 [Node.js 官网](https://nodejs.org/) 下载并安装。
- **npm**：Node.js 自带 npm，用于安装依赖包。
- **代码编辑器**：推荐使用 Visual Studio Code。

安装完成后，打开终端并运行以下命令，确保 Node.js 和 npm 已经正确安装：

bash
node -v
npm -v


👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)

## 创建 Windsurf 项目

首先，创建一个新的项目目录，并在该目录下初始化一个 Node.js 项目：

bash
mkdir my-blog
cd my-blog
npm init -y


接下来，安装 Windsurf 和一些必要的依赖：

bash
npm install windsurf express ejs mongoose body-parser


## 项目结构

我们的项目结构如下：


my-blog/
│
├── models/
│   ├── User.js
│   ├── Post.js
│
├── views/
│   ├── home.ejs
│   ├── register.ejs
│   ├── login.ejs
│   ├── create-post.ejs
│   ├── edit-post.ejs
│
├── public/
│   └── css/
│       └── style.css
│
├── app.js
└── package.json


## 构建博客首页

### 5.1 配置 Windsurf

首先，在 `app.js` 中配置 Windsurf：

javascript
const express = require('express');
const app = express();
const path = require('path');

app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));
app.use(express.static(path.join(__dirname, 'public')));

app.get('/', (req, res) => {
  res.render('home');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});


### 5.2 创建首页路由

在 `app.js` 中添加首页路由：

javascript
app.get('/', (req, res) => {
  res.render('home');
});


### 5.3 创建首页模板

在 `views` 目录下创建 `home.ejs` 文件：

html
<!DOCTYPE html>
<html>
<head>
  <title><%= title %></title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <h1>Welcome to My Blog</h1>
  <ul>
    <% posts.forEach(post => { %>
      <li>
        <h2><%= post.title %></h2>
        <p><%= post.content %></p>
        <p>By: <%= post.author %></p>
      </li>
    <% }) %>
  </ul>
</body>
</html>


### 5.4 添加样式

在 `public/css` 目录下创建 `style.css` 文件：

css
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

h1 {
  color: #333;
}


### 5.5 启动服务器

在终端中运行以下命令启动服务器：

bash
node app.js


打开浏览器，访问 `http://localhost:3000`，你应该会看到博客的首页。

## 用户注册与登录

### 6.1 配置会话

在 `app.js` 中配置会话管理：

javascript
const session = require('express-session');
app.use(session({
  secret: 'my-secret-key',
  resave: false,
  saveUninitialized: true,
}));


### 6.2 创建用户模型

在 `models` 目录下创建 `User.js` 文件：

javascript
const mongoose = require('mongoose');
const userSchema = new mongoose.Schema({
  username: String,
  password: String,
});
module.exports = mongoose.model('User', userSchema);


### 6.3 连接数据库

在 `app.js` 中连接 MongoDB：

javascript
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost:27017/my-blog');


### 6.4 用户注册

在 `app.js` 中添加用户注册路由：

javascript
app.post('/register', (req, res) => {
  const { username, password } = req.body;
  const user = new User({ username, password });
  user.save((err) => {
    if (err) return res.status(500).send(err);
    res.redirect('/login');
  });
});


### 6.5 用户登录

在 `app.js` 中添加用户登录路由：

javascript
app.post('/login', (req, res) => {
  const { username, password } = req.body;
  User.findOne({ username, password }, (err, user) => {
    if (err) return res.status(500).send(err);
    if (!user) return res.status(404).send('User not found');
    req.session.user = user;
    res.redirect('/');
  });
});


### 6.6 创建注册与登录模板

在 `views` 目录下创建 `register.ejs` 和 `login.ejs` 文件：

**register.ejs**:

html
<!DOCTYPE html>
<html>
<head>
  <title>Register</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <h1>Register</h1>
  <form action="/register" method="POST">
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required><br>
    <label for="password">Password:</label>
    <input type="password" id="password" name="password" required><br>
    <button type="submit">Register</button>
  </form>
</body>
</html>


**login.ejs**:

html
<!DOCTYPE html>
<html>
<head>
  <title>Login</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <h1>Login</h1>
  <form action="/login" method="POST">
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required><br>
    <label for="password">Password:</label>
    <input type="password" id="password" name="password" required><br>
    <button type="submit">Login</button>
  </form>
</body>
</html>


## 创建博客文章

### 7.1 创建文章模型

在 `models` 目录下创建 `Post.js` 文件：

javascript
const mongoose = require('mongoose');
const postSchema = new mongoose.Schema({
  title: String,
  content: String,
  author: String,
});
module.exports = mongoose.model('Post', postSchema);


### 7.2 创建文章路由

在 `app.js` 中添加创建文章的路由：

javascript
app.post('/create-post', (req, res) => {
  const { title, content, author } = req.body;
  const post = new Post({ title, content, author });
  post.save((err) => {
    if (err) return res.status(500).send(err);
    res.redirect('/');
  });
});


### 7.3 创建文章模板

在 `views` 目录下创建 `create-post.ejs` 文件：

html
<!DOCTYPE html>
<html>
<head>
  <title><%= title %></title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <h1>Create Post</h1>
  <form action="/create-post" method="POST">
    <label for="title">Title:</label>
    <input type="text" id="title" name="title" required><br>
    <label for="content">Content:</label>
    <textarea id="content" name="content" required></textarea><br>
    <button type="submit">Create Post</button>
  </form>
</body>
</html>


## 查看博客文章

### 8.1 显示所有文章

在 `app.js` 中添加显示所有文章的路由：

javascript
app.get('/', (req, res) => {
  Post.find({}, (err, posts) => {
    if (err) return res.status(500).send(err);
    res.render('home', { posts });
  });
});


### 8.2 更新首页模板

更新 `home.ejs` 文件以显示所有文章：

html
<!DOCTYPE html>
<html>
<head>
  <title><%= title %></title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <h1>Welcome to My Blog</h1>
  <ul>
    <% posts.forEach(post => { %>
      <li>
        <h2><%= post.title %></h2>
        <p><%= post.content %></p>
        <p>By: <%= post.author %></p>
      </li>
    <% }) %>
  </ul>
</body>
</html>


## 编辑与删除博客文章

### 9.1 编辑文章

在 `app.js` 中添加编辑文章的路由：

javascript
app.get('/edit-post/:id', (req, res) => {
  Post.findById(req.params.id, (err, post) => {
    if (err) return res.status(500).send(err);
    res.render('edit-post', { post });
  });
});

app.post('/edit-post/:id', (req, res) => {
  const { title, content } = req.body;
  Post.findByIdAndUpdate(req.params.id, { title, content }, (err) => {
    if (err) return res.status(500).send(err);
    res.redirect('/');
  });
});


### 9.2 删除文章

在 `app.js` 中添加删除文章的路由：

javascript
app.post('/delete-post/:id', (req, res) => {
  Post.findByIdAndDelete(req.params.id, (err) => {
    if (err) return res.status(500).send(err);
    res.redirect('/');
  });
});


### 9.3 创建编辑文章模板

在 `views` 目录下创建 `edit-post.ejs` 文件：

html
<!DOCTYPE html>
<html>
<head>
  <title><%= title %></title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <h1>Edit Post</h1>
  <form action="/edit-post/<%= post._id %>" method="POST">
    <label for="title">Title:</label>
    <input type="text" id="title" name="title" value="<%= post.title %>" required><br>
    <label for="content">Content:</label>
    <textarea id="content" name="content" required><%= post.content %></textarea><br>
    <button type="submit">Save Changes</button>
  </form>
</body>
</html>


## 部署项目

### 10.1 使用 PM2 部署

安装 `pm2`：

bash
npm install -g pm2


使用 PM2 启动应用：

bash
pm2 start app.js


### 10.2 部署到 Heroku

如果你有 Heroku 账号，可以将项目部署到 Heroku。首先，安装 Heroku CLI 并登录：

bash
heroku login


创建 Heroku 应用：

bash
heroku create


推送代码到 Heroku：

bash
git push heroku main


## 总结

现在，你已经成功使用 Windsurf 构建了一个完整的博客网站。通过这个项目，你学习了如何使用 Windsurf 创建路由、处理表单、管理会话、集成数据库等。希望这篇教程对你有所帮助，祝你在未来的开发旅程中取得更多成就！

如果你有任何问题或需要进一步的帮助，请随时联系我。继续加油！