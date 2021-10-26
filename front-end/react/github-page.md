# Github Page

### 安装 gh-pages

```
npm install --save gh-pages
或者
yarn add gh-pages
```

### 修改package.json 文件

```
  "homepage": "https://xxx.github.io/xxx/",
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  },
```

#### 添加内容

> homepage : github 仓库地址
>
> “predeploy“ ：“npm run build”   或者 “predeploy”: “yarn run dist”
>
> "deploy": "gh-pages -d build"   可拓展分支  "deploy": "gh-pages -b master -d build",



### 发布编译

```
npm run deploy 
或者
yarn run deploy
```
