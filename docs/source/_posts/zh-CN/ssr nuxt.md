---
title: NUXT-SSR 服务端渲染
top: false
cover: true
toc: true
comments: true
lang: zh-CN
summary: SSR服务端渲染
tags: [Nuxt, SSR]
categories: 服务端渲染教程
abbrlink: 241b6c32
date: 2020-02-22 19:11:21
updated:
password:
---
## nuxt安装

```JS
1.npx create-nuxt-app <项目名称> //相当于先全局安装 npm i nuxt 然年在创建一个项目 两个命令执行在一起了
```

## 路由

```JS
1.首先我们需要在你的page下的文件目录里去创建你的页面
2.然后我们在浏览器的地址栏里输入你的文件名称 就会跳转到你对应创建的文件的页面
3.当你在page文件夹里创建文件的时候 在.nuxt文件里的router.js文件就会给你自动添加进去你的路由跳转路径
```

## 视口/导航跳转

```JS
1.首先我们的项目创建完毕之后会自动生成一个layout的文件夹 里面有default.vue文件 这个相当于我们前台的app.vue文件
2.我们的视口标签是<nuxt> 相当于我们前台的router-view标签
3.跳转标签 是<nuxt-link to="/"> <NLink to=""> <n-link to=""> 相当于我们的router-link标签
```

## 预加载

```JS
1.ssr渲染 所有的页面都采用了预加载 跟我们前台渲染的懒加载差不多 第一个加载完毕之后 就会加载第二个
2.有的时候 我们有些页面 不需要预加载 只有在点击的时候才去加载
//哪个文件不需要预加载就把这个代码粘贴到那个文件即可
禁用预加载： <n-link no-prefetch>page not pre-fetched</n-link> 
```

## 路由跳转的传参

```JS
1.我们往往需要在跳转路由的时候带过去一些参数 以前前台渲染的时候 是需要在路由文件里配置{path:'/detail/:id'}
2.ssr渲染的时候 我们需要新建一个文件夹 文件夹的名字是detail(这里是你要跳转的路径) 然后文件夹里面的文件需要写成_id.vue
detail(文件夹)-_id.vue(文件)  等同于 {path:'/detail/:id'} 这个路由地址 如果存在多个参数  就多个嵌套即可
3.参数的获取还是和以前一样 用$route.params.id
```

## 嵌套视口

```JS
1.如果我们需要在一个页面下面有一个视口 当点击按钮的时候去跳转不同的页面 首先我们需要在当前页面 添加一个 路由视口标签
    <!-- 路由视图 -->
    <nuxt-child></nuxt-child>
2.新建一个跟你当前页面名称一样的文件夹 然后文件夹里面是你要在<nuxt-child></nuxt-child> 这个视口里要显示的页面
3.如果有参数的话 和上面路由跳转的传参的一样文件名称需要写_id.vue这种名称
4.跳转的按钮要的to属性 <nuxt-link :to="{name:'index-id',params:{id:good.id}}">
  这里的跳转路由的名字就需要写成 你当前文件的文件名 和你要跳转的文件的文件名
```

## 多套布局

```JS
1.前台渲染的时候 我们的登录页和后台管理首页用的都是一个router-view标签 
2.ssr渲染 我们可以分别指定每个页面的自己的用的布局 也就是类似router-view的文件
3.创建的项目默认情况下layout文件夹里会默认指定我们的page文件夹下的文件 
4.此时我们的登录页 和我们的首页 就用了两个不同的layout文件夹下的文件
5.只需要在layou文件夹下新建一个布局的blan.vue文件 
<template>
  <div>
    <nav>
      <nuxt />
    </nav>
  </div>
</template>
6.然后再需要渲染到这个布局里文件里 用layout:'blan' 即可 下面是登录页
<template>
  <div>
    登录页
  </div>
</template>

<script>
export default {
  layout: 'blan',
}
```

## head函数

```JS
1.我们的每个vue文件 都会有不同的nuxt的函数 
2.head() 主要这是每个页面文件的title 再浏览器的上面显示的title 对seo友好

head () {
    return {
      title: '课程列表',
      meta: [{ name: 'description', hid: 'description', content: 'set page meta' }],
      link: [{ rel: 'favicon', href: 'favicon.ico' }]
    }
  }
```

## 如何发送请求

```JS
1.首先这里我们还需要用axios去发送请求 这里需用装一下包 npm install @nuxtjs/axios -S nuxt整合axios的包
2.然后再nuxt.config.js里配置一下
```

## 配置：nuxt.config.js 

```JS
modules: [
'@nuxtjs/axios',
],
 
axios: {
proxy: true
},
//配置代理 下面是你要访问的服务的端口号 以api开头的 都去下面的服务器访问    
proxy: {
"/api": "http://localhost:8080"
},
```

## asyncData函数 获取异步数据

```js
1.asyncData 方法使得我们可以在设置组件数据之前异步获取或处理数据。
2.asyncData里的this不能用 因为这个是最早创建出来的你的this还没有没实例化 
3.形参里nuxt是传递的上下文 再这里我们能拿到很多有用的参数
//注意事项
asyncData 方法会在页面组件每次加载前被调用(是能使用再page文件里的组件)
asyncData 可以再服务端或者路由更新之前被调用
第一个参数被设定为当前页面的上下文对象
nuxt会将组件asyncData和data 方法返回的数据融合
由于asyncData再组件初始化前被调用的 所以不能通过this引用组件实例 因为那个时候vue组件实例还没有被创建
```

## 发送请求

```JS
<template>
  <div>
    <h2>商品列表</h2>
    <ul>
      <li v-for="good in goods" :key="good.id">
        <nuxt-link :to="{name:'index-id',params:{id:good.id}}">
          <span>{{ good.text }}</span>
          <span>￥{{ good.price }}</span>
          <button @click.prevent="addCart(good)">
            加购物车
          </button>
        </nuxt-link>
      </li>
    </ul>
  </div>
</template>


<script>
export default {
  async asyncData ({ $axios, error }) {
    const { ok, goods } = await $axios.$get('/api/goods')
    if (ok) {
      // 此处返回的数据会和data中的数据合并 
      return { goods }
    }
    // 重定向到错误页面
    error({ statusCode: 400, message: '数据查询失败' })
  },
  methods: {
    addCart () {}
  },
  head () {
    return {
      title: '课程列表',
      meta: [{ name: 'description', hid: 'description', content: 'set page meta' }],
      link: [{ rel: 'favicon', href: 'favicon.ico' }]
    }
  }
}
</script>
```

## 中间件

```JS
1.中间件 会在一个页面或者一组页面渲染之前运行我们自己定义的函数，常用于权限控制、校验等任务。
2.中间分为两种 
一种是页面的中间件 都会走这个中间件里的函数 比如我们要跳转到某个页面的时候 需要判断有没有登录 
二种是全局的 就需要在nuxt.config.js里的router选项去注册
3.中间件使用步骤 需要在middleware的文件夹去创建auth.js文件 然后注册页面在你需要使用的页面文件里的  
middleware: ['auth'] 即可
<template>
  <div>
    admin
  </div>
</template>

<script>
export default {
  middleware: ['auth'],
}
4.全局的只需要在nuxt.config.js里的router里去 middleware: ['auth'], 即可
```

## nuxt中的vuex的使用

```JS
1.不需要再去挂在index文件 只需要再store文件夹目录下创建对应的store模块即可 下面是user模块 
export const state = () => ({
  token: ''
})
export const mutations = {
  init (state, token) {
    state.token = token
  }
}
export const getters = {
  isLogin (state) {
    return !!state.token
  }
}
export const actions = {
  login ({ commit, getters }, u) {
      //利用nuxt提供的inject方法注入了$login方法 this指的是store的实例
    return this.$login(u).then(({ token }) => {
      if (token) {
        commit('SET_TOKEN', token)
      }
      return getters.isLogin
    })
  }
}
```

## nuxt插件注入 inject方法

```JS
1.这里我们用接口函数去举例 我们以前再页面掉接口函数的时候 都需要用import导入接口函数文件
2.nuxt这里给我们提供了插件的方法 只需要把你写的接口函数通过插件的形式注入到nuxt里面去 然后再文件的任何地方都可以调用了
```

```JS
//步骤
1.首先需要在plugin文件夹里创建你的接口文件(api-inject 文件名) 写入下面函数 inject是们nuxt自带的函数
// 参数1上下文，参数2 inject函数
export default ({ $axios }, inject) => {
  inject("login", user => {
    return $axios.$post("/api/login", user);
  });
};
2.然后再我们的nuxt.config.js文件里 再plugin选项里把我们方才创建的文件名字写入进去
  plugins: [
    "@/plugins/element-ui",
     //这里写入你刚才的文件名
    "@/plugins/api-inject",
    "@/plugins/interceptor",
  ],
以上你写的 login函数就能全局共用了   
```

## 配置请求拦截

```JS
1.配置和上面一样 还是需要再plugin里写请求拦截文件 
2.然后再nuxt.config.js里的plugin选项去注册
```

```JS
//配置 plugin-> interceptor.js
export default function ({ $axios, store }) {
  // onRequest是$axios模块提供的帮助方法
  $axios.onRequest((config) => {
    if (store.state.user.token) {
      // 将令牌附加到请求头
      config.headers.Authorization = 'Bearer ' + store.state.user.token
    }
    return config
  })

  //
}
//注册 nuxt.config.js -> plugin选项
plugins: [
    "@/plugins/element-ui",
    "@/plugins/api-inject",
    "@/plugins/interceptor",
  ],
```

## 关于cookie

```JS
1.首先我们的是服务端渲染 cookie是存放在服务端的 所以 当我们刷新页面的时候 你没有办法获取到服务端的cookie 还是会让你去登录页面
2.这里我们就需要安装一个可以再任何端都拿到cookie的模块cookie-universal-nuxt,
3.接下来我们就需要再store的根实例 也就是index.js文件里创建nuxtServerInit方法 Nuxt再初始化的时候调用他 会把我们想将服务端的一些数据传到客户端
```

```JS
1.安装 cookie-universal-nuxt 模块
2.再nuxt.config.js的modules选项里配置
modules: ['@nuxtjs/axios', 'cookie-universal-nuxt'],
3.store(文件夹) ->index.js 
export const actions = {
  // nuxtServerInit必须声明在根模块
  //   参数1是action上下文，参数2是组件上下文
  nuxtServerInit ({ commit }, ctx) {
    console.log(ctx)

    const token = ctx.app.$cookies.get('token')
    if (token) {
      console.log('nuxtServerInit: token:' + token)
      commit('user/SET_TOKEN', token)
    }
  }
}
当你每次刷新的时候 他就取cookie里的token然后 存放再vuex中 nuxtServerInit只能写再根vuex的实例 
```

