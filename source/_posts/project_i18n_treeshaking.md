---
title: 【项目方案】i18n国际化文件treeshaking
categories: 项目方案
date: 2020-03-28 16:00:55
tags:
  - 项目方案
  - 国际化
  - i18n
---

> i18n treeshaking 顾名思义，就如同 webpack 的 treeshaking（树摇晃） 操作一样，去掉没有使用的i18n国际化键值对。

好久没有更新博客了，去年下半年迷一样的状态，，一月放假，疫情又懒得动。

中国现在疫情已经到尾声了，但是各个省市依然有输入病例，大家千万不可以松懈，武汉加油，陕西加油，中国加油！！！

从三月底开始，会更新一些项目的解决方案，由平时项目比较关键的点提取出来，后续还会更新`微前端`实践方案。

正文开始！！！


## 思考（Why do this？）
1. 项目迭代版本，旧的国际化键值对因为业务变更等因素，很多使用的地方会被新业务覆盖甚至删除。
2. 人员变更，新旧人员更替，命名规则方式也很多不相同，有时候为了可维护，会写一套新的键值对。

以上两点随着日积月累，会造成旧的国际化键值对越来越多，维护越来越困难，所以迫切需要一个自动化的脚本来进行对比删除。

## 实现步骤设计
1. 第一步，抓取文件内的国际化键值对，拿所有的key做成一个数组，方便查找
2. 第二步，抓取指定文件目录下的js和vue文件
3. 第三步，进行对比，找到key的数组内任意一个，则计次1 ，统计所有计次，过滤掉所有计次为 0 的key
4. 第四步，抓出国际化数据过滤替换后写入

## 实现步骤代码
1. 获取i18n文件夹内所有的国际化文件的key

```js
/**
 * @description: 获取i18n文件夹内所有的国际化文件的key
 * @param {String} i18nDirPath i18n文件目录，只找第一层，不递归
 * @return {Object} i18nObj {fileName: ['xxx','xxx']}
 */
async function getI18NKey(i18nDirPath) {
  try {
    let i18nDir = fs.readFileSync(i18nDirPath);
    let i18nObj = {};
    i18nDir && i18nDir.forEach(fileName => {
      let filePath = path.join(i18nDirPath, fileName);
      let stats = fs.statSync(filePath);
      // 只是查找第一层的文件
      if (!stats.isDirectory() && treeShakingFileName.indexOf(fileName) > -1) {
        i18nObj[fileName] = [];
        let data = fs.readdirSync(filePath, {encoding: 'utf8'});
        data.replace(/\{([\s\S]+)\}/, function (match) {
          match.split('\n').forEach(row => {
            let rowText = row.trim();
            // 每行匹配双引号中间的key值
            let key = rowText.match(/(?:[\"|\'])([^\:]+)()(?:[\"|\'])/);
            key && i18nObj[fileName].push(key[1]);
          })
        })
      }
    })
    return i18nObj;
  } catch (e) {
    throw e;
  }
}
```

2. 获取指定文件目录下的js和vue文件路径

```js
/**
 * @description: 获取需要对比的所有文件路径
 * @param {String} rootDir
 * @return {Array} 文件夹下所有文件的路径
 */
async function getDiffFilePaths(rootDir) {
  try {
    let filePathDirs = readFileList(rootDir) || [];
    let arr = filePathDirs.filter(item => /.js/.test(item) && item.indexOf('\\lang') === -1 || /.vue/.test(item));
    return arr;
  } catch (e) {
    throw e;
  }
}


/**
 * @description: 递归查找文件夹下所有的文件列表
 * @param {String} dir 文件夹路径
 * @return {Array} fileList 文件路径列表
 */
function readFileList(dir, filesList = []) {
  const files = fs.readdirSync(dir);
  files.forEach((item, index) => {
    let fullPath = path.join(dir, item);
    const stat = fs.statSync(fullPath);
    if (stat.isDirectory()) {
      readFileList(path.join(dir, item), filesList); // 递归读取文件
    } else {
      filesList.push(fullPath);
    }
  })
  return filesList;
}
```

3. 进行对比，找到key的数组内任意一个，则计次1 ，统计所有计次，过滤掉所有计次为 0 的key

```js
/**
 * @description: 主入口
 * @param {type} 
 * @return: 
 */
async function main() {
  console.log('Searching for files, Please wait...');
  try {
    ...
    for (let i18nFileName in i18nkeyMap) {
      // 统计次数对象
      let i18nkeyObj = {};
      // 做个正则列表进行抓取
      let i18nRegList = i18nkeyMap[i18nFileName].map(key => {
        // 做正则时候顺便给i18nkeyObj做个初始化
        i18nkeyObj[key] = 0;
        return {key, reg: new RegExp(key, 'g')}
      });

      // 获取需要diff的文件路径
      let diffFilePaths = await getDiffFilePaths(diffDirPath);

      //按照文件路径的队列进行比对增加次数
      diffFilePaths.forEach((filePath, index) => {
        let fileData = fs.readFileSync(filePath, {encoding: 'utf8'});
        i18nRegList.forEach(() => {
          fileData.replace(reg, function () {
            i18nkeyObj[key] += 1;
          })
        })
      })

      let usedKeys = [];   // 已使用的keys
      let unUsedKeys = [];  // 未使用的keys
      // 统计出需要保留和删除的国际化keys
      for (let key in i18nkeyObj) {
        let times = i18nkeyObj[key];
        if (times !== 0) {
          console.log("Used key -- ", key);
          usedKeys.push(key);
        } else {
          console.log("Unused key -- ", key);
          unUsedKeys.push(key);
        }
      }
      ...
    }
  } catch (e) {
    throw e;
  }
}
```

4. 抓出国际化数据过滤替换后写入
因为有些项目需要缓存，有些项目会直接替换，所以这一步可变


## 完整代码
I18nTreeShaking.js

```js
/*
 * @Description: i18ntreeshaking js 去掉没有使用的i18n国际化键值对。
 * @Author: shifeng
 * @Email: shifeng199307@gmail.com
 * @Date: 2020-03-28 16:48:35
 */


const fs = require('fs');
const path = require('path');

// i18n文件目录，只找第一层，不递归
const i18nDirPath = path.join(__dirname, '../src/assets/i18n/lang');
// 对比的国际化文件
const treeShakingFileName = ['zh-CN.js'];
// 需要对比的文件目录
const diffDirPath = path.join(__dirname, '../src');

/**
 * 1. 第一步，抓取文件内的国际化键值对，拿所有的key做成一个数组，方便查找
 * 2. 第二步，抓取指定文件目录下的js和vue文件进行对比，找到key的数组内任意一个，则计次1
 * 3. 第三步，统计所有计次，过滤掉所有计次为 0 的key
 * 4. 第四步，抓出国际化数据过滤替换后写入
*/

/**
 * @description: 获取i18n文件夹内所有的国际化文件的key
 * @param {String} i18nDirPath i18n文件目录，只找第一层，不递归
 * @return {Object} i18nObj {fileName: ['xxx','xxx']}
 */
async function getI18NKey(i18nDirPath) {
  try {
    let i18nDir = fs.readFileSync(i18nDirPath);
    let i18nObj = {};
    i18nDir && i18nDir.forEach(fileName => {
      let filePath = path.join(i18nDirPath, fileName);
      let stats = fs.statSync(filePath);
      // 只是查找第一层的文件
      if (!stats.isDirectory() && treeShakingFileName.indexOf(fileName) > -1) {
        i18nObj[fileName] = [];
        let data = fs.readdirSync(filePath, {encoding: 'utf8'});
        data.replace(/\{([\s\S]+)\}/, function (match) {
          match.split('\n').forEach(row => {
            let rowText = row.trim();
            // 每行匹配双引号中间的key值
            let key = rowText.match(/(?:[\"|\'])([^\:]+)()(?:[\"|\'])/);
            key && i18nObj[fileName].push(key[1]);
          })
        })
      }
    })
    return i18nObj;
  } catch (e) {
    throw e;
  }
}

/**
 * @description: 获取需要对比的所有文件路径
 * @param {String} rootDir
 * @return {Array} 文件夹下所有文件的路径
 */
async function getDiffFilePaths(rootDir) {
  try {
    let filePathDirs = readFileList(rootDir) || [];
    let arr = filePathDirs.filter(item => /.js/.test(item) && item.indexOf('\\lang') === -1 || /.vue/.test(item));
    return arr;
  } catch (e) {
    throw e;
  }
}

/**
 * @description: 递归查找文件夹下所有的文件列表
 * @param {String} dir 文件夹路径
 * @return {Array} fileList 文件路径列表
 */
function readFileList(dir, filesList = []) {
  const files = fs.readdirSync(dir);
  files.forEach((item, index) => {
    let fullPath = path.join(dir, item);
    const stat = fs.statSync(fullPath);
    if (stat.isDirectory()) {
      readFileList(path.join(dir, item), filesList); // 递归读取文件
    } else {
      filesList.push(fullPath);
    }
  })
  return filesList;
}



/**
 * @description: 主入口
 * @param {type} 
 * @return: 
 */
async function main() {
  console.log('Searching for files, Please wait...');
  try {
    let i18nkeyMap = await getI18NKey(i18nDirPath);
    for (let i18nFileName in i18nkeyMap) {
      // 统计次数对象
      let i18nkeyObj = {};
      // 做个正则列表进行抓取
      let i18nRegList = i18nkeyMap[i18nFileName].map(key => {
        // 做正则时候顺便给i18nkeyObj做个初始化
        i18nkeyObj[key] = 0;
        return {key, reg: new RegExp(key, 'g')}
      });

      // 获取需要diff的文件路径
      let diffFilePaths = await getDiffFilePaths(diffDirPath);

      //按照文件路径的队列进行比对增加次数
      diffFilePaths.forEach((filePath, index) => {
        let fileData = fs.readFileSync(filePath, {encoding: 'utf8'});
        i18nRegList.forEach(() => {
          fileData.replace(reg, function () {
            i18nkeyObj[key] += 1;
          })
        })
      })

      let usedKeys = [];   // 已使用的keys
      let unUsedKeys = [];  // 未使用的keys
      // 统计出需要保留和删除的国际化keys
      for (let key in i18nkeyObj) {
        let times = i18nkeyObj[key];
        if (times !== 0) {
          console.log("Used key -- ", key);
          usedKeys.push(key);
        } else {
          console.log("Unused key -- ", key);
          unUsedKeys.push(key);
        }
      }

      // 最后按照将已使用的keys 的国际化键值对挑出来进行写入操作

      // do write
    }
  } catch (e) {
    throw e;
  }
}

module.exports = main().then(()=>{
  console.log('I18n file tree shaking success.');
})
```
