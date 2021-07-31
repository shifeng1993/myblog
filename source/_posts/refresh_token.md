---
title: 【token鉴权】无感刷新token
date: 2021-07-31 09:52:00
tags:
  - base
  - js
  - axios
  - promise
---

> 上个月，微信看到了一篇提到前端无感刷新token的问题，刚好项目中有这个场景，就想着实践并且记录一下

- 原文链接： https://juejin.cn/post/6983582201690456071
- 项目中主要使用到 oahth2.0鉴权，不知道的同学可以百度

- 在项目中进行实践，如上代码还有很多问题，也做了如下的修复项：
1. 修复了第一个报错的请求不会重试的问题
2. 修复了刷新token后没有重试的问题
3. 修复了刷新token微任务执行完成，没有清空重试队列的问题

- 脱敏后的js逻辑
  
```js
// request.js  请求拦截器

// 正在刷新的标记
let isRefreshing = false;
// 重试队列
let requests = [];

// response interceptor
service.interceptors.response.use(response => {
  // 这里是正常请求拦截器
}, error => {
  // 这里是报错拦截器
  const option = error.config
  const responseObj = error.response;
  switch (responseObj.status) {
    case 401:
      if (!isRefreshing) {
        isRefreshing = true;
        refreshToken().then(res => {
          if (res.success) {
            // 请求返回成功，需要按照业务刷新token 这里是oauth2.0鉴权标准，不存在敏感
            utils.setStorage('accessToken', res.data.accessToken);
            utils.setStorage('refreshToken', res.data.refreshToken);

            // 刷新token 后清空token失效后发出的请求
            requests.forEach(item => item(res.data.accessToken));

            // 需要在所有微任务队列执行完成后清空重试队列 settimeout加入宏任务队列
            setTimeout(() => {
              requests = [];
            }, 0)
          } else {
            // 如果接口不返回token，做的逻辑
            // 一般是清空token然后跳转到登录页
            return Promise.reject(new Error(res.errMsg));
          }
        }).catch(err => {
          // 如果抛出错误，做的逻辑
          // 一般是清空token然后跳转到登录页
          return Promise.reject(err);
        }).finally(() => {
          isRefreshing = false;
        })
      }

      // 返回等待态的promise  这里用return 就不要用break了
      return new Promise(resolve => {
        requests.push(token => {
          reslove(service({
            type: option.type,
            data: option.type !== 'GET' && !isFormData(option.data) ? JSON.parse(option.data) : option.data,
            url: option.url.replace(/Authorization=([^\&]+)/, () => 'Authorization=' + token),
            headers: {
              ...option.headers,
              Authorization: 'bearer ' + token
            }
          }))
        })
      })

    default:
      break;
  }
})
```