---
title: 项目中axios的封装
date: 2022-09-17 09:13:02
summary: 实际项目中axios的封装与应用
tags:
- axios
categories:
- [项目开发]
---

### 网络请求模块

功能：
- 请求头中自动携带token
- 未登录用户重定向到登录页
- 请求重连
- 支持取消请求（`"axios": ^0.22.0` ）
  
```js
// @/utils/request.js

import axois from 'axios';

const USER_NOT_LOGIN_CODE = '0000'; // 用户未登录
const USER_RESPONSE_CODE = '1000'; //  正常响应

// 创建axios实例
const service = axois.create({
    baseURL: `${process.env.VUE_APP_BASE_URL}/api`,
    timeout: 120000 // 请求超时时间
})

// request 拦截器设置
service.interceptors.request.use(
    config => {
        // 从localStorage中获取token
        const token = localStorage.getItem('token');
        if (token) {
            config.headers['token'] =  token // 请求头设置 token
        }
        return config
    },
    error => {
        Promise.reject(error)
    }
)

/**
 * 后端返回响应体结构
 * @param {string} code - 响应码
 * @param {boolean} success - 请求结果
 * @param {string} message - 提示信息
 * @param {object | array} result - 查询结果 
 */

// response 拦截器设置
service.interceptors.response.use(
    // HTTP状态码为 2xx 时进入到此回调
    response => {
        // 服务端返回的响应体
        const { success, result, code, message } = response.data

        // 根据响应码进行响应的逻辑操作
        // 未登录
        if (code === USER_NOT_LOGIN_CODE) {
            // 转到登录页
            window.location.href = 'xxxx/login';
        } else if (code !== USER_RESPONSE_CODE) { // 
            return Promise.reject(new Error(message || 'Error'))
        } else {
            return response.data
        }
    },
    // HTTP状态码非 2xx 时进入到此回调
    error => {
        let config = error.config;
        if (!config) return Promise.reject(error);

        // 请求重连功能
        const { retryCount = 0, retryDelay = 300, retryTimes = 2 } = config;
        // 记录已经重试的次数
        config.retryCount = retryCount;

        // 判断是否超过了重试次数
        if (retryCount >= retryTimes) {
            return Promise.reject(error);
        }
        // 增加重试次数
        config.retryCount++;
        // 延时处理后重新发起请求
        setTimeout(() => {
            service(config);
        }, retryDelay);
    }
)

export default service
```

### 业务请求模块
```js
// @/apis/user.js
import request from '@/utils/request';

const urls = {
    logout: '/user/logout', // 登出
    userList: '/user/list', // 用户列表
    upload: '/file/upload', // 上传文件
    download: '/file/download' // 下载文件 
}

// 登出
export function logout(params={}) {
    return request({
        method: 'get',
        url: urls.logout,
        params
    })
}

// 获取用户列表
export function getUserList(data, signal) {
    return request({
        method: 'post',
        url: urls.userList,
        data,
        signal
    })
}

// 上传文件
export function uploadFile(data) {
    return request({
        url: urls.upload,
        method: 'post',
        headers: {
            'Content-Type': 'multipart/form-data'
        },
        data
    })
},

// 下载文件
export function downloadFile(params) {
    return request({
        url: urls.download,
        method: 'get',
        responseType: 'blob', // 下载的文件为二进制blob对象，根据后端来决定
        params
    })
}
```

### 组件
```vue
<template>
    <div>用户模块</div>
</template>

<script>
import { logout, getUserList, uploadFile, downloadFile } from '@/apis/user'
export default {
    name: 'UserList',
    data() {
        return {
            pageInfo: {
                pageNo: 1,
                pageSize: 10
            }
        }
    },
    methods: {
        // 获取用户列表
        async getUserList() {
            const controller = new AbortController();

            // 3秒后自动取消请求
            setTimeout(() => {
                controller.abort();
            }, 3000);
            try {
                const response = await getUserList(this.pageInfo, controller.signal);
                console.log(response);
            } catch (error) {
                console.error(error);
            }
        },

        // 登出
        async logout() {
            try {
                const response = await logout();
                console.log(response);
            } catch (error) {
                console.error(error);
            }
        },

        // 上传
        async upload(file) {
            // 需要使用 FormData的格式上传文件
            const formData = new FormData()
            formData.append('file', file.file)

            try {
                const response = await uploadFile(formData)
            } catch (error) {
                console.error(error);
            }   
        },

        // 下载
        async download() {
            try {
                let res = await downLoadFile({ id: 1 });
                let URL = window.URL || window.webkitURL;
                const blobUrl = URL.createObjectURL(res);
                const a = document.createElement('a');
                a.style.display = 'none';
                a.href = blobUrl;
                a.setAttribute('download', decodeURI(`文件${new Date().getTime()}.xlsx`));
                if (a.download == 'undefined') {
                    a.setAttribute('target', '_blank')
                }
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                window.URL.revokeObjectURL(blobUrl);
            } catch(error) {
                console.log(error)
            }
        }
    }
}
</script>
```