```
npm install -g @vue/cli

vue --version

# 克隆项目
git clone https://github.com/PanJiaChen/vue-element-admin.git
# 进入项目目录
cd vue-element-admin
# 建议不要用 cnpm 安装 会有各种诡异的bug 可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org
# 本地开发 启动项目
npm run dev --port=8080 // vue-element-admin
# 构建项目（打包）
npm run build:prod

# 重装
删除 node_modules package-lock.json
# 插件安装也要带上--registry，不然会报错
npm install vuex-persistedstate --save --registry=https://registry.npm.taobao.org
```
element-admin涉及到`vue` `element` `promise` `axios` `vuex` `vue-router`
开发时调后端需要配置代理，前端代理，vue.config.js的devServer增加：
```
proxy: {
      [process.env.VUE_APP_BASE_API]: {
        target: 'http://192.168.126.128:9503',
        changeOrigin: true,
        pathRewrite: {
          ['^' + process.env.VUE_APP_BASE_API]: ''
        }
      }
    }
```
