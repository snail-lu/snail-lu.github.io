---
title: 项目中localStorage的封装
summary: localStorage在实际项目开发中的封装，增加过期时间和加密功能
date: 2022-12-12 21:49:08
tags:
- JavaScript
- localStorage
- sessionStorage
categories:
- [项目开发]
---

一般业务场景下，大部分人习惯直接使用 `localStorage`，并且使用明文存储；在一些特殊的需求情况下，可能需要设置过期时间或者对数据加密后再存储，这里特对`localStorage`进行二次封装，以方便项目中使用。

### 完整代码
```js
import CryptoJS from "crypto-js";

// 加密密钥
const SECRET_KEY = CryptoJS.enc.Utf8.parse("1235467812354678");
// 密钥偏移量
const SECRET_IV = CryptoJS.enc.Utf8.parse("1235467812354678");
/**
 * 判空
 * @param {*} value
 */
function isEmpty(value) {
  return value === "" || value === null || value === undefined;
}

/**
 * 加密方法
 * @param data
 * @returns {string}
 */
const encrypt = (data) => {
  if (typeof data === "object") {
    try {
      data = JSON.stringify(data);
    } catch (error) {
      console.log("encrypt error:", error);
    }
  }
  const dataHex = CryptoJS.enc.Utf8.parse(data);
  const encrypted = CryptoJS.AES.encrypt(dataHex, SECRET_KEY, {
    iv: SECRET_IV,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7
  });
  return encrypted.ciphertext.toString();
};

/**
 * 解密方法
 * @param data
 * @returns {string}
 */
const decrypt = (data) => {
  const encryptedHexStr = CryptoJS.enc.Hex.parse(data);
  const str = CryptoJS.enc.Base64.stringify(encryptedHexStr);
  const decrypt = CryptoJS.AES.decrypt(str, SECRET_KEY, {
    iv: SECRET_IV,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7
  });
  const decryptedStr = decrypt.toString(CryptoJS.enc.Utf8);
  return decryptedStr.toString();
};

/**
 * 存值
 * @param {*} key 键
 * @param {*} value 值
 * @param {*} expireTime 过期时间（相对时间），单位：秒
 * @param {*} isEncrypt 是否加密
 */
export function setStorage(key, value, expireTime = 3600, isEncrypt = false) {
  if (isEmpty(value)) value = null;
  if (isNaN(expireTime) || expireTime < 1)
    throw new Error("Expire Time must be a number");

  const data = {
    value: isEncrypt ? encrypt(value) : value, // 值
    createTime: Date.now(), // 存储时间戳
    expireTime: expireTime * 1000,
    isEncrypt
  };

  localStorage.setItem(key, JSON.stringify(data));
}

/**
 * 获取值
 * @param {*} key 键
 */
export function getStorage(key) {
  const value = localStorage.getItem(key);
  // key 不存在判断
  if (isEmpty(value)) return null;

  const obj = JSON.parse(value);
  let nowTime = Date.now();
  // 过期删除
  if (obj.expireTime && obj.createTime < nowTime - obj.expireTime) {
    removeStorage(key);
    return null;
  } else {
    // 未过期期间被调用，自动续期
    setStorage(key, obj.value);
    return obj.isEncrypt ? decrypt(obj.value) : obj.value;
  }
}

/**
 * 删除值
 * @param {*} key 键
 */
export function removeStorage(key) {
  localStorage.removeItem(key);
}

/**
 * 清空storage
 */
export function clearStorage() {
  localStorage.clear();
}

```

### 参考文章
[如何更好的封装localStorage工具类方法](https://www.zhouxuanyu.com/archives/103.html)