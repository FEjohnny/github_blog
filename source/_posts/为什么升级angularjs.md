---
title: 升级angularjs到angular
date: 2018-09-16 16:42:35
tags: [前端, 自动化, 测试]
---

&emsp;&emsp;升级angularjs到angular

<!--more-->

升级angularjs到angular：
组件化是趋势。
生态环境的支持。
性能提升。
至今许多企业仍然在使用 AngularJS 1.x 构建复杂的 Web 应用，通常这些项目都历史悠久，代码庞大，经历过许多不同开发人员的维护。尽管 AngularJS 曾经那么流行，但它毕竟是十年前的框架，现在的前端开发是属于 React、Vue 和 Angular 等现代框架的时代，虽然通过一些复杂的构建工具配置，也能享受到 Type（ES6）、Webpack 等好处，但仍然是戴着镣铐跳舞，力不从心。再加上 Angular 团队决定不再为它开发新功能，社区许多库也停止更新后，所有的重心都转移到了新版的 Angular


一、为什么要升级
        1. 关于angular
            a. 组件化是趋势。
            b. 生态环境的支持。
            c. 性能提升。
            d. 至今许多企业仍然在使用 AngularJS 1.x 构建复杂的 Web 应用，通常这些项目都历史悠久，代码庞大，经历过许多不同开发人员的维护。尽管 AngularJS 曾经那么流行，但它毕竟是十年前的框架，现在的前端开发是属于 React、Vue 和 Angular 等现代框架的时代，虽然通过一些复杂的构建工具配置，也能享受到 Type（ES6）、Webpack 等好处，但仍然是戴着镣铐跳舞，力不从心。再加上 Angular 团队决定不再为它开发新功能，社区许多库也停止更新后，所有的重心都转移到了新版的 Angular。
        2. 关于typscript
            a. 强类型检查
                使用强类型编写代码，可以使代码代码更加严谨。cannot read property ‘x’ of undefined或undefined is not a function是javascript运行时出现的错误，也是在线上发现的前端导致系统功能异常的错误，而目前我们所使用的方法几乎停留在运行时添加类型检测，但是这种做法比较累赘，而且一旦忘记可能又是一个bug。而恰恰typscript编译检查最明显的好处就是会减少很多类似bug，每一个变量都有类型约束，防止了我们申明了一个变量，在其他地方赋值时出现类型不同导致的问题，这样我们就只需要将尽力集中在逻辑处理上即可。
            b. 使Javascript面向对象的特性更加明显。
            c. 当你在使用 TypeScript 的时候，你其实已经在用最新的 JavaScript。


升级步骤：
模块化，引入webpack等打包工具
如果已经用了webpack，需要升级到v4+版本
webpack2升级到4的错误处理：
错误1：var outputName = compilation.mainTemplate.applyPluginsWaterfall('asset-path', outputOptions.filename, {TypeError: compilation.mainTemplate.applyPluginsWaterfall is not a function
解决：npm install html-webpack-plugin@3.2.0 -D
错误2：Chunk.entrypoints: Use Chunks.groupsIterable and filter by instanceof Entrypoint instead
解决：npm install extract-text-webpack-plugin@next
错误3：TypeError: Cannot read property 'babel' of undefined
解决：npm install babel-loader@7 -D
迁移到typescript
什么项目场景适合使用typescript?
正在使用babel编译的项目
无论是配置友好度,编译效率,语法兼容,都完全找不到理由选择babel
特别适合nodejs项目
完全兼容ES5,6...N版的代码,低成本迁徙,静态类型检测,接口interface定义,大大增强了代码健壮性
所有的大型JS项目
typescript是由 Anders Hejlsberg 大神(C#之父)开发,编译效率惊人
越大的项目,产生的作用越明显,完成迁徙之后,你基本可以立即找到埋的很深的坑
为js而生的开发工具 vscode ,微软出品的IDE,你懂的,觉得项目大了太卡,你可以试试
前端项目:react,vue,angular2
国内很火的react antd源码使用typescript编写
angular2的默认开发语言
typescript相关vue项目在github上也层出不穷
html5游戏项目
typescript是热门的egret(白鹭)引擎唯一开发语言，egret5.0.0 6月1号发布！
首先使用重命名工具renamex-cli将项目目录./src中的所有js文件后缀 批量改成.ts
npm i -g renamex-cli
//then
renamex start -p "src/**/*.js" -r "[name].ts" -t no
安装typescript
Npm I —save-dev typescript
添加tsconfig.json配置文件
修改webpack配置
然后出现了很多错，大部分都是找不到对应模块和对应属性不存在
修改所以import文件后缀为.ts
错误信息：'rootDir' is expected to contain all source；解决方法：tsconfig.json文件的rootDir设置有问题，其实只需要设置include就行了
TS1192: Module '"/Users/johnny/Documents/cs_workspace/client/node_modules/angular/index"' has no default export
导入*.tmpl.html， 新建html.d.ts文件，内容：declare module "*.html" {
    const template: string;
    export default template;
}
然后倒入的时候：import * as template from ‘./index.template.html’
1. 问题：ERROR in admin/admin.4f55e877.js from UglifyJs
Unexpected token: punc «.» [admin/admin.4f55e877.js:1013,19]
解决：

 Entrypoint undefined = extract-text-webpack-plugin-output-filename: