# Vue项目实战

## typescript相关配置

### 依赖

dependencies

| 依赖                   | 说明                    |
| :--------------------- | ----------------------- |
| vue-class-component    | 使用class语法写vue组件  |
| vue-property-decoretor | 在class基础上提供装饰器 |

devDependencies

| 依赖                             | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| typescript                       | 类型校验和转换js                                             |
| @vue/eslint-config-typescript    | 兼容eslint的ts校验规则                                       |
| @vue/cli-plugin-typescript       | 使用`typescript`+`ts-loader`+`fork-ts-checker-webpack-plugin`进行更快的类型检查 |
| @typescipt-eslint/parser         | 将ts转换为AST供ESLint校验                                    |
| @typescript-eslint/eslint-plugin | 使用eslint校验ts代码                                         |

### 配置

#### tsconfig.json

```json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "strict": true,
    "jsx": "preserve",
    "importHelpers": true,
    "moduleResolution": "node",
    "experimentalDecorators": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "sourceMap": true,
    "baseUrl": ".",
    "types": [
      "webpack-env"
    ],
    "paths": {
      "@/*": [
        "src/*"
      ]
    },
    "lib": [
      "esnext",
      "dom",
      "dom.iterable",
      "scripthost"
    ]
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "tests/**/*.ts",
    "tests/**/*.tsx"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

#### shims-vue.d.ts

用于ts识别vue文件, 声明导入vue模块都按照`Vueconstructor<Vue>`类型处理

```ts
declare module '*.vue' {
  import Vue from 'vue'
  export default Vue
}
```

#### shims-tsx.d.ts

为jsx声明一个命名空间并声明一些常用类型

```ts
import Vue, { VNode } from 'vue'

declare global {
  namespace JSX {
    // tslint:disable no-empty-interface
    interface Element extends VNode {}
    // tslint:disable no-empty-interface
    interface ElementClass extends Vue {}
    interface IntrinsicElements {
      [elem: string]: any
    }
  }
}
```

## Component写法

### [Optional API](https://cn.vuejs.org/v2/guide/typescript.html#%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95)

```js
import Vue from 'vue'
const Component = Vue.extend({
  // 类型推断已启用
})

const Component = {
  // 这里不会有类型推断，
  // 因为 TypeScript 不能确认这是 Vue 组件的选项
}
```

### [class](https://cn.vuejs.org/v2/guide/typescript.html#%E5%9F%BA%E4%BA%8E%E7%B1%BB%E7%9A%84-Vue-%E7%BB%84%E4%BB%B6)

基于[vue-class-component](https://github.com/vuejs/vue-class-component)和[vue-property-decorator](https://github.com/kaorun343/vue-property-decorator)

```js
// import Vue from 'vue'
// import Component from 'vue-class-component'
import { vue, Component, Prop } from 'vue-property-decorator'

// @Component 修饰符注明了此类为一个 Vue 组件
@Component({
  // 所有的组件选项都可以放在这里
  template: '<button @click="onClick">Click!</button>'
})
export default class MyComponent extends Vue {
  // 初始数据可以直接声明为实例的 property
  message: string = 'Hello!'

  @Prop(Number)
  a: nubmer = 1
  
  @Prop({ type: Boolean, default: false })
  b!: boolean
  
  @Prop([String, Boolean])
  c!: string | boolean

  // 组件方法也可以直接声明为实例的方法
  onClick (): void {
    window.alert(this.message)
  }
}
```

#### [增强类型](https://cn.vuejs.org/v2/guide/typescript.html#%E5%A2%9E%E5%BC%BA%E7%B1%BB%E5%9E%8B%E4%BB%A5%E9%85%8D%E5%90%88%E6%8F%92%E4%BB%B6%E4%BD%BF%E7%94%A8)

```js
// 1. 确保在声明补充的类型之前导入 'vue'
import Vue from 'vue'

// 2. 定制一个文件，设置你想要补充的类型
//    在 types/vue.d.ts 里 Vue 有构造函数类型
declare module 'vue/types/vue' {
// 3. 声明为 Vue 补充的东西
  interface Vue {
    $myProperty: string
  }
}
// ComponentOptions 声明于 types/options.d.ts 之中
declare module 'vue/types/options' {
  interface ComponentOptions<V extends Vue> {
    myOption?: string
  }
}

// 使用
// 全局 property
console.log(Vue.$myGlobal)

// 额外的组件选项
var vm = new Vue({
  myOption: 'Hello'
})
```

#### 装饰器语法

[ES6草案](https://www.bookstack.cn/read/es6-3rd/docs-decorator.md)

[typescript decorator](https://www.typescriptlang.org/docs/handbook/decorators.html#class-decorators)

## 代码规范

[JavaScript Standard Style](https://standardjs.com/), 适合个人开发
[Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
[Google JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html)  

[ESlint](https://cn.eslint.org/)

```js
module.exports = {
  root: true,
  env: {
    node: true
  },
  // 使用预定义规则组
  'extends': [
    'plugin:vue/essential',
    '@vue/standard',
    '@vue/typescript'
  ],
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off'
  },
  parserOptions: {
    parser: '@typescript-eslint/parser'
  }
}
```

- eslint-plugin-vue
  GitHub 仓库：https://github.com/vuejs/eslint-plugin-vue
  官⽅⽂档：https://eslint.vuejs.org/
  该插件使我们可以使⽤ ESLint 检查 .vue ⽂件的 <template> 和 <script>
  查找语法错误
  查找对[Vue.js指令](https://vuejs.org/v2/api/#Directives)的错误使⽤
  查找违反[Vue.js样式指南](https://vuejs.org/v2/style-guide/)的⾏为
- [@vue/eslint-config-standard](https://github.com/vuejs/eslint-config-standard)
  [eslint-plugin-standard](https://github.com/standard/eslint-plugin-standard)
  [JavaScript Standard Style](https://standardjs.com/)
- [@vue/eslint-config-typescript](https://github.com/vuejs/eslint-config-typescript)
  规则列表：https://github.com/typescript-eslint/typescripteslint/tree/master/packages/eslint-plugin#supported-rules  

### [自定义ES Lint](https://cn.eslint.org/docs/user-guide/configuring)

改变一个规则, id必须为以下之一:

- "off" 或 0 - 关闭规则
- "warn" 或 1 - 开启规则，使⽤警告级别的错误： warn (不会导致程序退出)
- "error" 或 2 - 开启规则，使⽤错误级别的错误： error (当被触发的时候，程序会退出)  

#### 配置规则

```js
// 规则可以带额外选项, 使用数组字面量指定, 数组第一总是规则的严重程度
{
	"rules": {
    "rule1": "off",
    "rule2": "error",
    "rule3": ["error", "double"],
    // 使用`plugin/rule`的形式配置定义在插件中的规则
    // 插件名称为包名去掉`eslint-plugin-`前缀
    "plugin1/rule4": "error"
	}
}
// 可以在文件中使用注释启用规则
/* eslint rule1: "off", rule2: "error", plugin1/rule4: ["error", "double"] */
```

#### cache

ts的编译结果会保留在`node_modules/.cache`目录下, 所以偶尔会发生代码更改了但eslint没报错的情况, 这时候需要手动删除cache重启服务

#### VS Code配置

```json
{
    "files.autoSave": "off",
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "vue-html",
        "vue"
    ],
    "eslint.run": "onSave",
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    }
}
```

## [Element UI](https://element.eleme.cn/#/zh-CN)

`npm i element-ui  `

```js
// main.ts
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(ElementUI)
```

### 样式处理

```
src/styles
|-- index.less // 全局样式, 在入口模块加载生效
|-- mixin.less // 公共的mixin混入, 将重复的样式封装为mixin混入到复用的地方
|-- reset.less // 重置基础样式
|-- variables.less // 公共样式变量
```

### 全局样式

```
<style lang="scss" scoped>
@import "~@/styles/variables.scss"

.text {
  color: $success-color;
}
</style>
```

#### [通过css-loader加载到全局](https://cli.vuejs.org/zh/guide/css.html#%E5%90%91%E9%A2%84%E5%A4%84%E7%90%86%E5%99%A8-loader-%E4%BC%A0%E9%80%92%E9%80%89%E9%A1%B9)

```js
// ./vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      scss: {
        additionalData: `@import "~@/variables.scss";`
      }
    }
  }
}
```

## 后端接口代理

### [devServer](https://cli.vuejs.org/zh/config/#devserver)

```
devServer: {
    proxy: {
      '/api': {
        target: '<url>',
        ws: true,
        changeOrigin: true
      },
      '/foo': {
        target: '<other_url>'
      }
    }
  }
}
```

### 路由配置

配置component时注意使用懒加载, 并使用注释别名, 否则会编译为以数字为名的模块

```
{
  name: 'advert-space',
  path: '/advert-space',
  component: () =>
    import(
      /* webpackChunkName: 'advert-space' */ '@/views/advert-space/index.vue'
    )
},
```

### 用户接口

#### [登录](http://113.31.105.128/front/doc.html#/edu-front-boot/%E7%94%A8%E6%88%B7%E6%8E%A5%E5%8F%A3/loginUsingPOST)

`axios`默认data format是`application/json`, 但登录接口是`x-www-form-urlencoded`, 搜索[官方文档](https://github.com/axios/axios#using-applicationx-www-form-urlencoded-format), 有两种方法:

1. 使用[`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)创建data, 但需要polifill

   ```js
   const params = new URLSearchParams();
   params.append('param1', 'value1');
   params.append('param2', 'value2');
   axios.post('/foo', params);
   ```

2. 使用qs库

   ```js
   import qs from 'qs';
   const data = { 'bar': 123 };
   // axios.post('/foo', qs.stringify(data));
   const options = {
     method: 'POST',
     headers: { 'content-type': 'application/x-www-form-urlencoded' },
     data: qs.stringify(data),
     url,
   };
   axios(options);
   ```
   

## [表单验证](https://element.eleme.io/#/zh-CN/component/form#zi-ding-yi-xiao-yan-gui-ze)

- 为`form`绑定`rules`
  - rule规则使用[async-validator](https://github.com/yiminghe/async-validator)
- 将formdata双向绑定到form上
- 将`item.prop`设置为rule的校验字段
- 为from绑定ref, 然后调用`validate`和`resetFields`方法

## 用户登录

使用vuex共享userinfo

在store初始化和set的时候读取/写入`window.localstorage`以实现持久化

### 权限控制

为每个页面通过[路由元信息](https://router.vuejs.org/zh/guide/advanced/meta.html#%E8%B7%AF%E7%94%B1%E5%85%83%E4%BF%A1%E6%81%AF)配置权限, 并使用全局[路由守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#%E5%AF%BC%E8%88%AA%E5%AE%88%E5%8D%AB)判断权限

```js
// 路由守卫中一定要调用 next，否则页码无法展示
router.beforeEach((to, from, next) => {
  // to.matched 是一个数组（匹配到是路由记录）, 只要任意一个路由需要身份验证
  if (to.matched.some(record => record.meta.requiresAuth)) {
    if (!store.state.user) {
      next({
        name: 'login',
        query: { // 通过 url 传递查询字符串参数
          redirect: to.fullPath // 把登录成功需要返回的页面告诉登录页面
        }
      })
    } else {
      next() // 允许通过
    }
  } else {
    next() // 允许通过
  }
})
```

#### 动态绑定资源路径时, 需要使用`require`方法

`<img :src="userInfo.img || require('@/assets/default-avatar.png')"`

### 拦截请求设置Auth

axios可以使用interceptors为请求和响应设置拦截器

```js
request.interceptors.request.use(config => {
  if (store.state.user && store.state.user.access_token) {
    config.headers.Authorization = store.state.user.access_token
  }
  return config
})
```

## access token

token设置比较短的过期时间, 为了安全

### 过期处理方法

1. 请求前, 判断token是否过期, 是则挂起请求, 刷新token后再继续
   - 在请求前拦截, 节省请求
   - 需要后端提供额外的过期时间字段
   - 使用本地时间判断是否过期, 拦截会失败
2. 请求后, 根据response判断是否过期, 是则刷新后重试请求
   - 不需要额外字段
   - 多消耗一次请求

推荐使用方式二

### axios拦截器

```js
request.interceptors.response.use(
  res => res,
  async err => {
    if (err.response) {
      // 收到响应但状态码错误
      const { status } = err.response
      switch (status) {
        case 400:
          Message.error('请求参数错误')
          break
        case 401:
          // token无效, 未提供, 过期等
          if (await tryUpdateToken()) {
            return request(err.config)
          }
          return Promise.reject(err)
        case 403:
          Message.error('没有权限, 请联系管理员')
          break
        case 404:
          Message.error('请求资源不存在, 请联系管理员')
          break
        default:
          Message.error('服务端错误, 请联系管理员')
          break
      }
    } else if (err.request) {
      // 未收到响应
      Message.error('请求超时, 请重试')
    } else {
      // 在设置请求时触发错误
      Message.error(`请求失败: ${err.message}`)
    }
    return Promise.reject(err)
  }
)

const tryUpdateToken = async () => {
  const tokenForRefresh =
    (store.state.user && store.state.user.refresh_token) || null
  if (!tokenForRefresh) {
    redirectLogin()
    return false
  }

  try {
    const {
      data: { success, content, message }
    } = await refreshToken(tokenForRefresh)
    if (!success) {
      throw message
    }

    store.commit('setUser', JSON.parse(content))
    return true
  } catch (error) {
    store.commit('setUser', null)
    redirectLogin()
    return false
  }
}
```

### refresh token

用户登录时有`access token`和`refresh token`, `refresh token`用于获得新的`access token`, 且仅能使用一次

所以需要在拦截器中设置请求缓存队列, 当有多个请求需要刷新token时, 只进行一次刷新, 成功后再继续执行队列中的所有请求

```js
let refreshingToken = false
const waitingRequests: any[] = []
case 401:
  // token无效, 未提供, 过期等
  if (!refreshingToken) {
    refreshingToken = true
    tryRefreshToken()
      .then(refreshed => {
        waitingRequests.forEach(({ resolve, reject }) =>
          refreshed ? resolve() : reject()
        )
      })
      .finally(() => {
        refreshingToken = false
        waitingRequests.splice(0)
      })
  }
  return new Promise((resolve, reject) =>
    waitingRequests.push({
      resolve: () => resolve(request(err.config)),
      reject: () => reject(new Error('refresh token failed'))
    })
  )
case 403:
  ...
```

## 权限管理

- 用户分配多个角色, 取所有角色并集的权限和资源
- 不同角色可看到的菜单不一样
- 不同角色可操作的资源不一样
- 资源就是接口, 对不同资源的增删改查操作权限



## 组件和路由解耦

router配置中传入 `props: true`, 这样url的params就会绑定到组件的props中

## 富文本编辑器

- [ckeditor5](https://ckeditor.com/ckeditor-5/)
- [quill](https://quilljs.com/)
- [medium-editor](https://github.com/yabwe/medium-editor), 更新频率慢
- [wangeditor](http://www.wangeditor.com/), 国人维护
- [tinymce](https://www.tiny.cloud/)
- [ueditor](https://github.com/fex-team/ueditor), 百度, 不再维护












































