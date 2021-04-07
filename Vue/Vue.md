#	Vue

### 1.创建项目目录，在目录中使用命令

~~~~
vue init webpack vue01
~~~~

### 2.进入目录，进行命令

1. 安装vue-router

   ~~~
   npm install vue-router --save-dev
   ~~~

2. 安装element-ui

   ~~~
   npm i element-ui -S
   ~~~

3. 安装依赖

   ~~~
   npm install
   ~~~

4. 安装sass加载器

   ~~~
   cnpm install sass-loader node-sass --save-dev
   ~~~

5. 启动测试

   ~~~
   npm run dev
   ~~~

   

## vue-cli 3.x创建项目

~~~xml
vue create vue05
~~~



#### 1.创建目录结构

![image-20200426151358689](C:\Users\Victor\AppData\Roaming\Typora\typora-user-images\image-20200426151358689.png)



#### 2.导入css总体样式文件

​		![image-20200426151508290](C:\Users\Victor\AppData\Roaming\Typora\typora-user-images\image-20200426151508290.png)fig.js



#### 3.创建 ==vue.config.js== 引用路径起别名

![image-20200426152857178](C:\Users\Victor\AppData\Roaming\Typora\typora-user-images\image-20200426152857178.png)





#### 4.创建  ==.editorconfig==  文件

![image-20200426153354638](C:\Users\Victor\AppData\Roaming\Typora\typora-user-images\image-20200426153354638.png)





#### 5.安装路由 -> 对路由进行配置 -> export导出路由 -> 在main.js中挂载 -> 导入组件，配置映射关系 

~~~java
npm install vue-router --save
~~~

~~~java
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const routes = [
 {
    path: "/",
    component: Home
  },
]

const router = new VueRouter({
  routes: routes,
  mode: 'history'
})


export default router
~~~

子路由

~~~java
routes: [
    {
      path: '/',
      name: 'home',
      component: home,
      children: [
        {
          path: '/',
          name: homeChild,
          component: homeChild
        },
        {
          path: '/info',
          name: blogInfo,
          component: blogInfo
        },
        {
          path:'/me',
          name:me,
          component: me
        }
      ]
    }
  ]
~~~





#### 6.安装 axios -> 封装network模块

~~~java
npm install axios --save
~~~

~~~java
import axios from 'axios'

export function request(config) {
  /*创建实例*/
  const instance1 = axios.create({
    timeout: 5000
  })

  /*请求拦截器*/
  instance1.interceptors.request.use(config => {
    console.log(config);
    return config;
  }, error => {
    console.log(error);
  })

  /*响应拦截器*/
  instance1.interceptors.response.use(res => {
    console.log(res);
    return res;
  }, error => {
    console.log(error);n
  })


  return instance1(config);
}

~~~





#### 7.使用request

~~~java
import {request} from "./request";

export function login(username,password) {
    return request({
      url:"/login",
      params:{
        username:username,
        password:password
      }
    })
}
~~~



### 8.导入使用axios

~~~java
import * as login from '../../network/login'
    
login.login(this.loginForm.username, this.loginForm.password).then(res => {
            console.log(res);
            if (res.data.status == 200) {
              window.sessionStorage.setItem("token", res.data.token);
              window.sessionStorage.setItem("username", res.data.username);
              this.$message.success("登录成功")
              this.$router.replace('/');
            } else {
              this.$message.error("用户名或密码错误")
            }
          }).catch(err => {
            console.log(aerr);
          })
~~~



### 9.使用element-ui

~~~java
npm i element-ui -S
~~~



##### 在main.js中全局引入

~~~java
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI)
~~~





this.$router.push('/home')