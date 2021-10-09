# bubblewu.github.io
记录日常学习：https://bubblewu.github.io/

## 项目文件介绍


参考主题：
- [volantis](https://volantis.js.org/)

## hexo命令
- 常见命令：
```js
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
```
- 命令缩写：
```js
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```
- 组合命令：
```js
hexo s -g #生成并本地预览
hexo d -g #生成并上传
```

## 部署
```js
# hexo部署 hexo clean && hexo deploy
hexo clean
hexo generate
hexo deploy
# hexo g -d
```

## 新建文章/页面
```
# 新建文章
hexo new post -p java/test "test title"
# post: 建立post类型模板的文章，source/_post为默认主目录，可省略不写
# -p --path: 指定路径和文件名称 java/ 路径，test为.md名称
# "test title": 文章标题title

# 新建页面
hexo new page -p about/me "about me"
# source/ 为主目录，page不可省略
```

## 添加新文章部署步骤
```
进入项目，并选择存放文件目录：cd /Users/xxx/env/hexo/hexo-blog/source/_posts/Java/并发
生成静态页面至public目录：hexo generate
开启预览访问端口（默认端口4000，'ctrl + c'关闭server）：hexo server
部署到github：hexo deploy
```
