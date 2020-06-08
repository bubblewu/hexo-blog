---
title: antdvp.vue-cli
tags: vue
categories: vue
date: 2020-05-07 01:32:13
---

ant-design-vue-pro 使用脚手架 vue-cli 创建项目

<!-- more -->

## vue create <my-project-name>
```bash
#npm install -g @vue/cli  vue --version  @vue/cli 4.3.1
vue create <my-project-name>

# Vue CLI v4.3.1
# ? Please pick a preset: Manually select features
# ? Check the features needed for your project: Babel, Router, Vuex, CSS Pre-processors, Linter
# ? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
# ? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Less
# ? Pick a linter / formatter config: Prettier
# ? Pick additional lint features: Lint on save
# ? Where do you prefer placing config for Babel, ESLint, etc.? In dedicated config files
# ? Save this as a preset for future projects? Yes
# ? Save preset as: a-d-v-p
```
## Less、history
```bash
# Less: css加载器使用less，方便之后做主题颜色修改配置
# history: 最终路由中不带#号，漂亮的url路由
# In dedicated config files: 配置文件不堆积在package.json中；使用单独的配置文件vue.confi.js需手动新建
```
## npm run serve
```bash
# 验证第一步是否创建成功
cd <my-project-name>
npm run serve

# 第二步 安装插件ant-design-vue 和 moment
npm i ant-design-vue moment
```
## vue.config.js
```bash
# webpack配置之less-load引入
mkdir vue.config.js
# 修改vue.config.js文件，添加css内容
module.exports = {
  // less-load
  css: {
    loaderOptions: {
      less: {
        // 这里的选项会传递给 less-loader
        javascriptEnabled: true
      }
    }
  }


# 组件和样式按需加载，优化减小前端加载流量
# babel.config.js 文件配置
module.exports = {
  presets: ["@vue/cli-plugin-babel/preset"],
  plugins: [
    [
      "import",
      { libraryName: "ant-design-vue", libraryDirectory: "es", style: true }
    ] // `style: true` 会加载 less 文件
  ]
};
# 安装插件 babel-plugin-import
npm i --save-dev babel-plugin-import
```

![advp.png](https://gitee.com/wongqingbin/Frieza/raw/master/image/advp.png)

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}