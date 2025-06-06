# user-center-web

[toc]

## 技术栈

- Vue3:主流前端框架
- Vue-CLI脚手架：快速启动项目
-  Ant Design 组件库：快速开发UI 界面
- Axios请求库：向后端发送请求
- Pinia状态管理：维护前端全局数据
- 前端工程化:ESLint +Prettier +TypeScript，保证前端项目开发规范



## Web前端项目初始化

### 确认环境

nodejs16.0以上

```shell
node -v
```

### 创建项目

使用Vue-CLI脚手架快速创建Vue3的项目:
为什么选择该脚手架？

1. 常用的标准脚手架，开源、并且star数多
2. 目前进入维护模式，相对稳定，对低版本的Node兼容性好，不容易出现因为环境不同而导致的问题
3. 相对轻量，整合了一些前端项目开发常用的工具，并且可以按需选取该脚手架自动整合了vue-router路由、TypeScript、前端工程化等库

创建脚手架：

```shell
npm install -g @vue/cli
```

创建Vue项目：

```shell
vue create user-center-web
```

运行Vue项目

```shell
cd user-center-web
npm run serve
```

### 引入组件库

本项目使用的是Ant Design of Vue组件库

> 官方文档：https://www.antdv.com/docs

安装组件

```shell
npm i --save ant-design-vue@4.x
```

引入依赖

```javascript
import { createApp } from 'vue';
import Antd from 'ant-design-vue';
import App from './App';
import 'ant-design-vue/dist/reset.css';

const app = createApp(App);

app.use(Antd).mount('#app');
```

### 开发规范

建议遵循Vue组合式API而不是选项式API，这样编写更自由。

### 全局通用布局

#### 1.基础布局结构

在layouts目录下新建一个布局BasicLayout.vue，在App.vue全局页面入口文件中引入。

App.vue：

```javascript
<script setup lang="ts">
import BasicLayOut from './layouts/BasicLayOut.vue';

</script>

<template>
    <div id="app">
        <BasicLayOut>
            <router-view></router-view>

        </BasicLayOut>

    </div>

</template>

<style>

</style>
```
选用Ant Design组件库的Layout组件，先把【上中下】布局编排好，然后再填充内容。

BasicLayout.vue:

```javascript
<template>
  <div id="basicLayout">
    <a-layout style="min-height: 100vh">
      <a-layout-header>Header</a-layout-header>
      <a-layout-content>Content</a-layout-content>
      <a-layout-footer>Footer</a-layout-footer>
    </a-layout>
  </div>
</template>

<script setup lang="ts"></script>

```



#### 2.全局底部栏

通常用于展示版权信息：

```html
            <a-layout-footer class="footer">
                <a href="https://github.com/chengfushi">
                    GitHub by Chengfu Shi
                </a>
            </a-layout-footer>
```

样式

```css
#basicLayOut .footer {
    background: #efefef;
    padding: 16px;
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    text-align: center;
}
```

#### 3.全局顶部栏

由于顶部栏的开发相对复杂，可以基于Ant Design的菜单组件来创建GlobalHeader全局顶部栏组件，组件统一放mponents目录中。
先直接复制现成的组件示例代码到GlobalHeader中即可。

```typescript
<template>
  <a-menu v-model:selectedKeys="current" mode="horizontal" :items="items" />
</template>
<script lang="ts" setup>
import { h, ref } from 'vue';
import { MailOutlined, AppstoreOutlined, SettingOutlined } from '@ant-design/icons-vue';
import { MenuProps } from 'ant-design-vue';
const current = ref<string[]>(['mail']);
const items = ref<MenuProps['items']>([
  {
    key: 'mail',
    icon: () => h(MailOutlined),
    label: 'Navigation One',
    title: 'Navigation One',
  },
  {
    key: 'app',
    icon: () => h(AppstoreOutlined),
    label: 'Navigation Two',
    title: 'Navigation Two',
  },
  {
    key: 'sub1',
    icon: () => h(SettingOutlined),
    label: 'Navigation Three - Submenu',
    title: 'Navigation Three - Submenu',
    children: [
      {
        type: 'group',
        label: 'Item 1',
        children: [
          {
            label: 'Option 1',
            key: 'setting:1',
          },
          {
            label: 'Option 2',
            key: 'setting:2',
          },
        ],
      },
      {
        type: 'group',
        label: 'Item 2',
        children: [
          {
            label: 'Option 3',
            key: 'setting:3',
          },
          {
            label: 'Option 4',
            key: 'setting:4',
          },
        ],
      },
    ],
  },
  {
    key: 'alipay',
    label: h('a', { href: 'https://antdv.com', target: '_blank' }, 'Navigation Four - Link'),
    title: 'Navigation Four - Link',
  },
]);
</script>


```

在基础布局中引入顶部导航栏

```typescript
<a-layout-header class="header">
  <GlobalHeader />
</a-layout-header>
```

修改一下全局默认样式，这里用优先级更高的CSS来进行覆盖

```css
#basicLayout .header {
  padding-inline: 20px;
  margin-bottom: 16px;
  color: unset;
  background: white;
}
```

接下来修改GlobalHeader.vue

1. 给菜单外套一层元素，用于整体控制样式：

    ```typescript
    <div id="globalHeader">
      <a-menu v-model:selectedKeys="current" mode="horizontal" :items="items" />
    </div>
    ```

2. 根据我们的需求修改菜单配置，key为要跳转的URL路径：

    ```typescript
    import {h, ref} from "vue";
    import {CrownOutlined, HomeOutlined} from "@ant-design/icons-vue";
    import {MenuProps} from "ant-design-vue";
    //导航栏当前选中项
    const items = ref<MenuProps["items"]>([
        {
            key: "/",
            icon: () => h(HomeOutlined),
            label: "主页",
            title: "主页",
        },
        {
            key: "/user/login",
            label: "用户登录",
            title: "用户登录",
        },
        {
            key: "/user/register",
            label: "用户注册",
            title: "用户注册",
        },
        {
            key: "/admin/userManage",
            icon: () => h(CrownOutlined),
            label: "用户管理",
            title: "用户管理",
        },
        {
            key: "others",
            label: h(
                "a",
                {href: "https://chengfushi.blog.csdn.net/", target: "_blank"},
                "个人博客"
            ),
            title: "个人博客",
        },
    ]);
    ```

3. 完善全局顶部栏，左侧补充网站图标和标题。
    先把logo.png放到src/assets目录下，替换掉原本的默认Logo:

    修改GlobalHeader代码，补充Html

    ```typescript
    <a-col flex="200px">
       <div class="title-bar">
            <img class="logo" src="../assets/logo.png" alt="logo">
            <div class="title">耄耋用户中心</div>
       </div>
     </a-col>
    ```

    通过CSS调整样式

    ```css
    .title-bar {
        display: flex;
        align-items: center;
    }
    
    .title {
        color: black;
        font-size: 18px;
        margin-left: 16px
    }
    
    .logo {
        height: 48px;
        width: 48px;
    }
    ```

4. 完善顶部导航栏，右侧展示当前用户的登录状态（暂时用登录按钮代替）:

    ```html
    <div class="user-login-status">
      <a-button type="primary" href="/user/login">登录</a-button>
    </div>
    ```

    

5. 优化导航栏的布局，采用栅格组件的自适应布局（左中右，左侧右侧宽度固定，中间菜单栏自适应）

    ```html
        <div id="globalHeader">
            <a-row :wrap="false">
                <!--左边网站logo-->
                <a-col flex="200px">
                    <div class="title-bar">
                        <img class="logo" src="../assets/logo.png" alt="logo">
                        <div class="title">耄耋用户中心</div>
                    </div>
                </a-col>
    
                <!--中部导航栏-->
                <a-col flex="auto">
                    <a-menu
                        v-model:selectedKeys="current"
                        mode="horizontal" :items="items"
                        @click="doMenueClick"
                    />
                </a-col>
    
                <!--用户登录状态-->
                <a-col flex="100px">
                    <div class="user-login-status">
                        <a-button type="primary" href="/user/login" ghost>登录</a-button>
                    </div>
                </a-col>
            </a-row>
        </div>
    ```

    

### 路由

目标：点击菜单项后，可以跳转到对应的页面；并且刷新页面后，对应的菜单自动高亮。

#### 1.修改路由配置：

修改router/index.ts文件的routes配置，定义我们需要的页面路由，每个path对应一个component（要加载的组件)，
暂时可以先用HomeView代替。

```typescript
import {createRouter, createWebHistory, RouteRecordRaw} from 'vue-router'
import HomeView from '../views/HomeView.vue'

const routes: Array<RouteRecordRaw> = [
    {
        path: '/',
        name: 'home',
        component: HomeView
    },
    {
        path: '/about',
        name: 'about',
        // route level code-splitting
        // this generates a separate chunk (about.[hash].js) for this route
        // which is lazy-loaded when the route is visited.
        component: () => import(/* webpackChunkName: "about" */ '../views/AboutView.vue')
    },
    {
        path: '/user/login',
        name: 'login',
        component: HomeView,
    },
    {
        path: '/user/register',
        name: 'register',
        component: HomeView,
    },
    {
        path: '/admin/userManage',
        component: HomeView,
    }

]

const router = createRouter({
    history: createWebHistory(process.env.BASE_URL),
    routes
})

export default router
```

#### 2.给GlobalHeader的菜单组件绑定跳转事件:

```typescript
import {useRouter} from "vue-router";

const router = useRouter();

// 路由跳转实现
const doMenueClick = ({key}: { key: string }) => {
    router.push(
        {
            path: key,
        }
    )
}
```

修改Html

```html
<a-col flex="auto">
   <a-menu
      v-model:selectedKeys="current"
      mode="horizontal" :items="items"
      @click="doMenueClick"
   />
</a-col>
```

#### 3.同步路由的更新到菜单项高亮
同步高亮原理：

1. 点击菜单时，Ant Design组件已经通过v-model绑定current变量实现了高亮。
2. 刷新页面时，需要获取到当前URL路径，然后修改current变量的值，从而实现同步。使用VueRouter的afterEach路由钩子实现，每次改变路由或刷新页面时都会自动更新current的值，从而实现高亮。

```typescript
const router = useRouter();
// 当前选中菜单
const current = ref<string[]>([]);
// 监听路由变化，更新当前选中菜单
router.afterEach((to, from, failure) => {
    current.value = [to.path];
});

```



### 请求

引入Axios请求库
一般情况下，前端只负责界面展示和动效交互，尽量避免写复杂的逻辑；当需要获取数据时，通常是向后端提供的接口发
送请求，由后端执行操作（比如保存数据）并响应数据给前端。
前端如何向后端发送请求呢？最传统的方式是使用AJAX技术。但其代码有些复杂，我们可以使用第三方的封装库，来简
化发送请求的代码，比如主流的请求工具库Axios。

#### 1.请求工具库

安装请求工具类Axios
官方文档:https://axios-http.com/docs/intro
代码：

```shell
npm install axios
```

#### 2.全局自定义请求
需要自定义全局请求地址等，参考Axios官方文档，编写请求配置文件request.ts。包括全局接口请求地址、超时时
间、自定义请求响应拦截器等。

响应拦截器的应用场景：我们需要对接口的通用响应进行统一处理，比如从response中取出data;或者根据code去集
中处理错误。这样不用在每个接口请求中都去写相同的逻辑。比如可以在全局响应拦截器中，读取出结果中的data，并校验code是否合法，如果是未登录状态，则自动登录。

示例代码如下，其中withCredentials：true一定要写，3否则无法在发请求时携带Cookie，就无法完成登录。
代码如下：

```typescript
import axios from "axios";

const myAxios = axios.create({
    baseURL: "http://localhost:8080",
    timeout: 10000,
    withCredentials: true,
});

// Add a request interceptor
myAxios.interceptors.request.use(
    function (config) {
        // Do something before request is sent
        return config;
    },
    function (error) {
        // Do something with request error
        return Promise.reject(error);
    }
);

// Add a response interceptor
myAxios.interceptors.response.use(
    function (response) {
        // Any status code that lie within the range of 2xx cause this function to trigger
        // Do something with response data
        console.log(response);

        const {data} = response;
        console.log(data);
        // 未登录
        if (data.code === 40100) {
            // 不是获取用户信息接口，或者不是登录页面，则跳转到登录页面
            if (
                !response.request.responseURL.includes("user/current") &&
                !window.location.pathname.includes("/user/login")
            ) {
                window.location.href = `/user/login?redirect=${window.location.href}`;
            }
        }
        return response;
    },
    function (error) {
        // Any status codes that falls outside the range of 2xx cause this function to trigger
        // Do something with response error
        return Promise.reject(error);
    }
);

export default myAxios;
```



#### 3.自定义请求

在src目录下新建api/user.ts，存放所有和用户有关的API接口。按照Axios的规则，根据后端接口信息编写对应的代
码即可：

```typescript
import myAxios from "@/request";

/**
 * 用户注册
 * @param params
 */
export const userRegister = async (params: any) => {
    return myAxios.request({
        url: "/user/register",
        method: "POST",
        data: params,
    });
};

/**
 * 用户登录
 * @param params
 */
export const userLogin = async (params: any) => {
    return myAxios.request({
        url: "/user/login",
        method: "POST",
        data: params,
    });
};

/**
 * 用户注销
 * @param params
 */
export const userLogout = async (params: any) => {
    return myAxios.request({
        url: "/user/logout",
        method: "POST",
        data: params,
    });
};

/**
 * 获取当前用户
 */
export const getCurrentUser = async () => {
    return myAxios.request({
        url: "/user/current",
        method: "GET",
    });
};

/**
 * 获取用户列表
 * @param username
 */
export const searchUsers = async (username: any) => {
    return myAxios.request({
        url: "/user/search",
        method: "GET",
        params: {
            username,
        },
    });
};

/**
 * 删除用户
 * @param id
 */
export const deleteUser = async (id: string) => {
    return myAxios.request({
        url: "/user/delete",
        method: "POST",
        data: id,
        // 关键点：要传递 JSON 格式的值
        headers: {
            "Content-Type": "application/json",
        },
    });
};
```

然后可以尝试在任意页面代码中调用API：

### 全局状态管理

什么是全局状态管理？
所有页面全局共享的变量，而不是局限在某一个页面中。
适合作为全局状态的数据：已登录用户信息（每个页面几乎都要用）
Pinia是一个主流的状态管理库，使用更简单，可参考入门文档。

1. 引入pinia

```javascript
import { createApp } from "vue";
import App from "./App.vue";
import router from "./router";
import { createPinia } from "pinia";
import Antd from "ant-design-vue";
import "ant-design-vue/dist/reset.css";

const pinia = createPinia();
createApp(App).use(pinia).use(Antd).use(router).mount("#app");

```

2. 定义状态。在src/store目录下定义user模块，定义了用户的存储、远程获取、修改逻辑：

```javascript
import { defineStore } from "pinia";
import { ref } from "vue";
import { getCurrentUser } from "@/api/user";

export const useLoginUserStore = defineStore("loginUser", () => {
  const loginUser = ref<any>({
    username: "未登录",
  });

  async function fetchLoginUser() {
    const res = await getCurrentUser();
    if (res.data.code === 0 && res.data.data) {
      loginUser.value = res.data.data;
    }
  }

  function setLoginUser(newLoginUser: any) {
    loginUser.value = newLoginUser;
  }

  return { loginUser, setLoginUser, fetchLoginUser };
});
```





