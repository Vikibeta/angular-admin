
# Angular-Admin

[![GitHub stars](https://img.shields.io/github/stars/surmon-china/angular-admin.svg?style=flat-square)](https://github.com/surmon-china/angular-admin/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/surmon-china/angular-admin.svg?style=flat-square)](https://github.com/surmon-china/angular-admin/issues)
[![GitHub forks](https://img.shields.io/github/forks/surmon-china/angular-admin.svg?style=flat-square)](https://github.com/surmon-china/angular-admin/network)
[![GitHub last commit](https://img.shields.io/github/last-commit/google/skia.svg?style=flat-square)](https://github.com/surmon-china/angular-admin)
[![](https://badge.juejin.im/entry/58a5f28eac502e006cbed110/likes.svg?style=flat-square)](https://juejin.im/entry/58a5f28eac502e006cbed110/detail)
[![Twitter](https://img.shields.io/twitter/url/https/github.com/surmon-china/angular-admin.svg?style=flat-square)](https://twitter.com/intent/tweet?url=https://github.com/surmon-china/angular-admin)

My blog admin client by Angular, Bootstrap4.

The project forked from [ng2-admin](https://akveo.github.io/ng2-admin/).

The api of this project is accompanied by the [nodepress](https://github.com/surmon-china/nodepress) program, and if you want to run up, you need to install [nodepress](https://github.com/surmon-china/nodepress).

If you only need to demonstrate the project locally, you need to comment on the following lines of code:
- [src/app/app.component.ts:54](https://github.com/surmon-china/angular-admin/blob/master/src/app/app.component.ts#L54)
- [src/app/app.component.ts:114](https://github.com/surmon-china/angular-admin/blob/master/src/app/app.component.ts#L114)
- [src/app/app.component.ts:120](https://github.com/surmon-china/angular-admin/blob/master/src/app/app.component.ts#L120)

[CHANGELOG](https://github.com/surmon-china/angular-admin/blob/master/CHANGELOG.md)


# Screenshots

**标签管理**

![](https://raw.githubusercontent.com/surmon-china/angular-admin/master/screenshots/tag-list.png)

**发布文章**

![发布文章](https://raw.githubusercontent.com/surmon-china/angular-admin/master/screenshots/submit-article.png)

**全局设置**

![](https://raw.githubusercontent.com/surmon-china/angular-admin/master/screenshots/setting.png)

**文章列表**

![](https://raw.githubusercontent.com/surmon-china/angular-admin/master/screenshots/article-list.png)

**评论管理**

![](https://raw.githubusercontent.com/surmon-china/angular-admin/master/screenshots/comment.png)

**markdown编辑器**

![](https://raw.githubusercontent.com/surmon-china/angular-admin/master/screenshots/markdown.png)

# 程序结构（踩坑记录）

## 资源服务
   - 一个类
   - 一堆方法
   - 返回promise
   - 用第三方组件配合来实现res/err的处理
   - 定制化的http服务是需要自己封装的，如果你想实现拦截器，Auth 控制的话，必须封装，项目中用了三方的[angular2-jwt](https://github.com/auth0/angular2-jwt)

## 自定义组件的双向绑定如何实现
   - 框架基本都同质化了，和vue实现方式相差无几，但实现原理是不同的，Angular 内置了 Zone.js 对所有全局的异步 API 进行了高级包装，并抽象出了异步任务管理层，用以驱动 Angular 的数据检测
   - "盒子里的香蕉" [(ngModel)] = [ngModelChange] + (model)
   - 一句话：用既定内置的api做预期的事
   - [看这里有实现文件](https://github.com/surmon-china/angular-admin/blob/master/src/app/theme/components/baMarkdownEditor/markdownEditor.component.ts)

## 路由管理和拦截
   - 路由管理可以集中内聚管理，也可以模块化碎片化，项目中两者都有
   - 在app根组件中，注入router对象订阅event的事件流，从而实现"拦截"，此方法无法中断
   - 因为我暂时没找到管方提供的类似拦截的api，欢迎pr

## auth验证
   - 本地token库[angular2-jwt](https://github.com/auth0/angular2-jwt)会验证两点，1：是否存在 2：是否有效 3：是否过期
   - 可选一些配置，见作者[github](https://github.com/auth0/angular2-jwt)
   - 在登录页，需要使用ng2的http服务，否则无法发起请求，原因：
        1：token不存在，存在也被验证为失效，不然怎么来登录页的，怎么初始化的
        2：angular2-jwt只要验证不通过就return false了，当然你可以配置
   - 这也就是前面所说的http服务可以有多个，什么地方用哪一个，取决于你自己

## 组件/模块与页面构成
   - 组件和vue一样，以文件夹为内聚单位，而不是单文件
   - 比组件高一个维度但又不是一个维度的元素：模块，模块可以理解为一个完整功能的内聚单位
   - 组件、过滤器、指令、公共服务、验证...

## 数据流
   - 略复杂...我也没搞很明白
   - 简单的话，就to promise，项目中是这么做的

## 管道过滤器
   - 简单，都一样

## 自定义指令
   - 简单

## 七牛上传组件的封装
   - 无奈还是使用了官方提供的js插件，具体看官方
   - token从服务端sdk生成获取
   - 拿token上传文件
   - 上传后的后续处理：用image对象获取图片，成功后再emit事件和更新数据，可以保证，我们看到成功提示那一刻，图片已经成功加载到本地了
   - 由于设置了防盗链导致本地调试403错误

## 表单验证构造和使用
   - 依赖form里面的类、基本类、验证类、...
   - 基本的构造方法是怎样的，看官方文档
   - 自定义正则验证规则，看官方文档

## 模块/组件/服务之间的关系
   - 模块为完整功能的内聚单位，可包含多种其他元素，如组件、路由、服务、指令...
   - 组件包含view，类似view + 控制器映射，负责view的直接逻辑
   - service属数据工具性质，本项目中用来获取/管理资源/状态，但没有使用service实现数据共享，仅仅是封装了resource部分
   - 原计划将部分service抽象为公共服务，但是多次注入会被多次实例化，导致无法订阅数据流，暂没找到更好方法

## 一些要注意的坑
   - 变量声明（声明前的判断，和调用时可能出现的错误）
   - ```(<any>window).aaa = {}```，这里不能用global判断，global在webpack中是可用的，本项目也依赖了虚拟的dom对象，编译后global关键字被自动替换为window引入，所以只能如上写法，当然还有一种try catch的方法也能做到
   - 在项目中使用jquery，或其他js库，记得声明变量，`declare var jQuery:any;`
   - 内置了一些如ngOnInit这样的周期性钩子函数，不要冲突
   - cnpm和npm安装文件结构不一样，会导致重复引用无法编译的问题，主要是由于cnpm的版本管理机制不一样导致的，cnpm是空文件夹+快捷方式的方法乱搞，所以遇到模块引用的错误，一般删掉对应文件夹，用npm重装就可以了


# 执行命令

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).

```


# 目录结构
```
angular-admin/
   |
   ├──config/                    * webpack构建配置
   │   |
   │   ├──head-config.common.js  * 配置index.html的头部元信息
   │   │
   │   ├──helpers.js             * 配置文件的辅助函数
   │   │
   │   ├──webpack.dev.js         * webpack开发环境配置
   │   │
   │   ├──webpack.prod.js        * webpack生产环境配置
   │   │
   │   ├──webpack.test.js        * webpack测试环境配置
   │   │
   │   └──html-elements-plugin/  * html elements plugin
   │
   ├──src/                       * 源文件最终编译为javascript
   │   |
   │   ├──custom-typings.d.ts    * 第三方模块的自定义类型
   │   │
   │   ├──index.html             * html入口文件
   │   │
   │   ├──main.browser.ts        * app入口文件
   │   │
   │   ├──polyfills.browser.ts   * 浏览器降级支持polyfills file
   │   │
   │   ├──vendor.browser.ts      * 需要集中打包的模块（一般为核心/第三方模块）
   │   │
   │   ├──app/                   * application code - our working directory
   │   │   │
   │   │   ├──app.component.ts   * app入口组件
   │   │   │
   │   │   ├──app.loader.ts      * 应用程序加载阶段)、，初始化时用的样式
   │   │   │
   │   │   ├──app.menu.ts        * 页面的菜单/路由
   │   │   │
   │   │   ├──app.module.ts      * app入口模块
   │   │   │
   │   │   ├──app.routes.ts      * app路由
   │   │   │  
   │   │   ├──global.state.ts    * 组件之间交换的全局应用状态
   │   │   │
   │   │   ├──environment.ts     * 环境提供
   │   │   │
   │   │   ├──app.scss           * 样式入口文件 
   │   │   │
   │   │   ├──pages/             * application pages components, place where you can create pages and fill them with components
   │   │   │
   │   │   └──theme/             * 模板/全局通用组件、指令、过滤器、样式
   │   │
   │   └──assets/                * 静态资源
   │
   │
   ├──tslint.json                * typescript lint 配置
   │
   ├──typedoc.json               * typescript文档生成器
   │
   ├──tsconfig.json              * config that webpack uses for typescript
   │
   │──package.json               * what npm uses to manage it's dependencies
   │
   │──Dockerfile                 * Docker镜像生成文件
   │
   │──build.sh                   * Docker构建命令
   │
   │──.travis.yml                * Travis 持续集成配置
   │
   │──.gitignore                 * Git忽略文件配置
   │
   │──.editorconfig              * 编码风格配置
   │
   └──.bootstraprc               * bootstrap-loader文件配置

```
