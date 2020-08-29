# 配置 mocha + nyc

## Node 14 + Mocha 8 以及使用 import 的方法

1. 安装最新版本 Node(v14.0.0)
2. 在 `package.json` 中增加 `"type": "module"`

## Node 14 + Mocha 8 + nyc 以及使用 es5 语法

1. 安装 nyc
2. 在 `package.json` 中增加 `"coverage": "nyc mocha"`
3. 新建 `.nycrc` 文件配置 nyc

   ```json
   {
     "all": true,
     "include": ["src/*.js"]
   }
   ```

## Node 14 + Mocha 8 + nyc + Babel 7 以及使用最新的 JavaScript 语法

1. 安装依赖并创建新脚本

   ```json
   {
     "scripts": {
       "test": "mocha --require @babel/register",
       "coverage": "nyc --reporter=html --reporter=text mocha --require @babel/register"
     },
     "devDependencies": {
       "@babel/preset-env": "^7.11.0",
       "@babel/register": "^7.10.5",
       "mocha": "^8.1.1",
       "nyc": "^15.1.0"
     }
   }
   ```

2. 配置 babel `.babelrc`:

   ```json
   {
     "presets": ["@babel/preset-env"]
   }
   ```

3. 配置 nyc `.nycrc`:

   ```json
   {
     "all": true,
     "include": ["src/*.js"]
   }
   ```
