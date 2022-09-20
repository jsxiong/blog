---
title: vue中的keep-alive组件
date: 2022-09-20 21:19:37
tags: vue
category: vue
---


## 前言

在日常的业务开发中，经常会碰到这么一种需求。

从一个页面跳转到另一个页面后，再跳转回来时。要保留跳转之前的一些内容（比如搜索条件之类的）。

这个需求当然是可以通过Session Storage来实现，不过还是比较麻烦。 使用vue中的keep-alive组件就比较快乐了


## 基础使用

- 使用起来其实简单。在router-view的外面包一层keep-alive组件就行了。 这是vue的内置组件， 直接使用就好了

```html
    <keep-alive >
      <router-view></router-view>
    </keep-alive>
```

- 当然，并不是所有的页面都需要去缓存的，这个时候就可以使用这个组件提供的两个属性 **include** 和 **exclude** 
  - include - 只有名称匹配的组件会被缓存, 字符串或正则表达式
  - exclude - 任何名称匹配的组件都不会被缓存, 字符串或正则表达式

#### 通过传入include/exclude判断是否需要缓存

```html
  <!-- -逗号分隔字符串 --> -->
    <keep-alive include="PageA,PageB" exclude="PageC">
      <router-view />
    </keep-alive>

      <!-- 正则表达式 (使用v-bind绑定) -->
    <keep-alive :include="/a|b/">
      <router-view />
    </keep-alive>

    <!-- 3.数组(使用v-bind绑定)-->
    <keep-alive :include="['PageA', 'PageB']">
      <router-view />
    </keep-alive>
```


 > tips： include，exclude首先匹配组件自身的name选项，如果没有name，则匹配它的局部注册名称 (父组件 components 选项的键值)。匿名组件不能被匹配


- 也可以结合路由来区分是否需要缓存，可以动态配置，更灵活
  
#### 通过与路由结合来判断是否需要缓存

在路由的meta信息里面加一个是否需要缓存的标识，然后需要缓存的和不需要缓存入口分开成两个router-view

```js
  {
    path: '/home',
    name: 'Home',
    component: () => import('@/views/home/index'),
    meta: { title: '工作台', keepAlive: true }
  }
```

``` html
    <keep-alive>
      <router-view v-if="$route.meta.keepAlive"></router-view>
    </keep-alive>
    <router-view v-if="!$route.meta.keepAlive"></router-view>
```

#### 配置完成后keep-alive不生效的话，有以下可能

1. 路由对应页面的文件没有写name，或者页面文件的name和路由配置的name不一致
2. v-if判断写错地方，示例:
  ```js
    // 错误代码
    <keep-alive v-if="$route.meta.keepAlive">
      <router-view></router-view>
    </keep-alive>
    <router-view v-else></router-view>


    <!-- 正确代码 -->
    <keep-alive>
      <router-view v-if="$route.meta.keepAlive"></router-view>
    </keep-alive>
    <router-view v-if="!$route.meta.keepAlive"></router-view>
  ```


#### 生命周期

如果使用了keep-alive缓存，那么进入页面或者离开页面时将不会触发create、mounted、beforeDestroy。 那么改怎么办呢，可以使用以下这两个生命后期

**activated：** 在 keep-alive 组件激活时调用（替代create、mounted）

**deactivated**：在 keep-alive 组件停用时调用 （替代beforeDestroy）
