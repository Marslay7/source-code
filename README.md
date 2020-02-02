### vue 源码学习

#### 搭建调试环境

- 项目获取地址：https://github.com/vuejs/vue.git
- 安装依赖：yarn / npm install
  - 如果遇到 node-pre-gyp -g 报错，先全局安装：npm install -g node-pre-gyp -g
- 安装 rollup(js 模块打包器)：yarn add rollup / npm i -g rollup
- 修改 package.json 中 dev 脚本：添加--sourcemap
- 执行 dev 脚本：yarn dev / npm run dev

#### 调试技巧

- 打开指定文件：ctrl + p / command + p
- 断点：开发者模式 Sources 下
  - 单步执行 --- <img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gbgtg296hrj303k02cwed.jpg" style="zoom:25%;" />
  - 单步执行函数(进入函数) --- <img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gbgtg23tt1j301w02oglg.jpg" style="zoom:25%;" />
- 查看调用栈(执行流程)：<img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gbgtn30zg9j30j409mjs8.jpg" style="zoom:33%;" />
- 定位源文件所在位置：右键点击 Reveal in sidebar，在侧边栏查找文件位置

#### 项目目录

- dist 发布目录
- examples 范例，里面有测试代码
- flow 针
- packages 核心代码之外的独立库
- scripts 构建脚本
- src 源码
  - compiler 编译器相关
  - core 核心代码
    - components 通用组件，如 keep-alive
    - global-api 全局 api
    - instance 构造函数等
    - observer 响应式相关
    - vdom 虚拟 DOM 相关
- types ts 类型声明，对 flow 的类型声明

#### 术语解释

- runtime：仅包含运行时，不包含编译器(意味着不能用 templete 去写字符串模版)
- common：common.js 规范，用于 webpack1
- esm：ES 模块，主要用于 webpack2+
- umd：universal module definition，兼容 cjs 和 amd 规范，用于浏览器

#### 学习流程

- src/platforms/web/entry-runtime-with-compiler.js

  - 入口文件：覆盖\$mount，执行模版解析和编译工作

- src/platforms/web/runtime/index.js

  - 定义\$mount()方法

- src/core/index.js

  - 定义了全局 api

- src/core/instance/index.js

  - 定义构造函数

  - 定义实例方法

    - stateMixin(Vue); // $set,$delete,\$watch

      eventsMixin(Vue); // $emit,$on,$off,$once

      lifecycleMixin(Vue); // \_update,$forceUpdate(),$destroy()

      renderMixin(Vue); // \_render(),\$nextTick

- src/core/instance/init.js

  - 初始化方法\_init 定义的地方

    - initLifecycle(vm);

      initEvents(vm);

      initRender(vm);

      callHook(vm, "beforeCreate");

      initInjections(vm); // resolve injections before data/props

      initState(vm);

      initProvide(vm); // resolve provide after data/props

      callHook(vm, "created");

  - 组件的创建是自上而下的，挂载是自下而上的。

- src/core/instance/state.js

  - initData 获取 data，设置代理，启动响应式 observe

- src/core/observer/index.js

  - 创建 Observer 观察者

#### 初始化流程

new Vue()

- 调用 init

=> this.\_init(options)

- 初始化各种属性方法

=> \$mount

- 调用 mountComponent()

=> mountComponent()

- 声明 updateComponent()
- 创建 Watcher(vue2+后，一个组件一个 Watcher)

=> \_render()

- 获取虚拟 dom

=> \_update()

- 把虚拟 dom 转换为真实 dom

#### Vue2.0 响应式缺点

- 反复地递归遍历，性能会受影响

- api 不统一

  - 对于数组的操作

    - items = [1,2]

      items.length = 0 // 无效

      items[0] = 'aaa' // 无效

      Vue.set(items,0,'abc') // ok
