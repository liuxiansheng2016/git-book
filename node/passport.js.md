---
description: >-
  是一个非常流行的 Node.js 身份验证中间件，它简化了在应用程序中实现多种身份验证策略的过程。Passport 可以与 Express 或其他兼容
  Connect 的框架一起使用，并支持本地认证（如用户名和密码）、OAuth、OpenID 等多种认证方式。
---

# Passport.js

## **主要特点**

* 模块化设计：Passport 本身只是一个核心库，具体的认证策略由单独的模块提供。这意味着你可以根据需要选择和组合不同的认证方法。
* 易于集成：Passport 集成了 Express 和 Connect 中间件系统，因此可以很方便地添加到现有的应用中。
* 丰富的插件生态：有大量现成的策略可供选择，涵盖了从社交媒体登录（如 Facebook、Twitter）到企业级身份提供商（如 LDAP、SAML）的各种需求。
* 灵活性：无论是简单的用户注册/登录功能还是复杂的多因素认证，Passport 都能灵活应对。

首先，你需要安装 Passport 和你想要使用的认证策略模块。例如，为了使用本地认证策略（基于用户名和密码），你可以这样安装：

npm install passport passport-local express-session --save

express-session 是用来管理会话的中间件，对于大多数情况下都是必需的，因为它帮助 Passport 在请求之间保持用户的身份信息。

除了本地认证，Passport 还支持 OAuth 认证。例如，如果你想让用户通过 GitHub 登录，你可以安装并配置 passport-github 策略。

npm install passport-github --save

### **使用 passportlocal**

```javascript
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const User = require('./models/User'); // 假设您有一个 User 模型

passport.use(new LocalStrategy(
  async (username, password, done) => {
    try {
      const user = await User.findOne({ username });
      if (!user) {
        return done(null, false, { message: 'Incorrect username.' });
      }
      const isMatch = await user.comparePassword(password); // 假设您有一个 comparePassword 方法
      if (!isMatch) {
        return done(null, false, { message: 'Incorrect password.' });
      }
      return done(null, user);
    } catch (err) {
      return done(err);
    }
  }
));

passport.serializeUser((user, done) => {
  done(null, user.id);
});

passport.deserializeUser(async (id, done) => {
  try {
    const user = await User.findById(id);
    done(null, user);
  } catch (err) {
    done(err);
  }
});

module.exports = passport;

```

### 配置passport

```javascript
const express = require('express');
const session = require('express-session');
const passport = require('./auth'); // 引入您刚刚配置的 Passport

const app = express();

app.use(express.urlencoded({ extended: false }));
app.use(session({ secret: 'your_secret_key', resave: false, saveUninitialized: false }));
app.use(passport.initialize());
app.use(passport.session());

app.post('/login', passport.authenticate('local', {
  successRedirect: '/dashboard',
  failureRedirect: '/login',
  failureFlash: true
}));

app.get('/logout', (req, res) => {
  req.logout();
  res.redirect('/');
});

function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
}

app.get('/dashboard', ensureAuthenticated, (req, res) => {
  res.send('Welcome to your dashboard!');
});

app.listen(3000, () => {
  console.log('Server is running on http://localhost:3000');
});

```

### 配置DB

```javascript
// 文件路径: /path/to/models/User.js
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');

const userSchema = new mongoose.Schema({
    username: { type: String, required: true, unique: true },
    password: { type: String, required: true }
});

userSchema.pre('save', async function (next) {
    if (!this.isModified('password')) {
        return next();
    }
    const salt = await bcrypt.genSalt(10);
    this.password = await bcrypt.hash(this.password, salt);
    next();
});

userSchema.methods.comparePassword = function (candidatePassword) {
    return bcrypt.compare(candidatePassword, this.password);
};

const User = mongoose.model('User', userSchema);
module.exports = User;

```
