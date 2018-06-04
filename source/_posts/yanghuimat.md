---
title: 【js】杨辉三角js实现
categories: JS
date: 2017-08-06
tags:
  - JS
  - 杨辉三角
---
杨辉三角本质可以看为一个以对角线为轴一少半是0的矩阵。

既然是矩阵，可以获取输入中行秩列秩中较大的进行杨辉三角的初始化，这样就会永远包裹输入。

初始化之后就是根据输入的行列进行遍历取出对应的值。


```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>杨辉三角</title>
</head>

<body>
  <div>
    请输入行数和列数(用空格隔开)：
    <input type="text" id="numInput">
  </div>
  <div>
    <button onclick="calc()">得出结果</button>
    <span id="result"></span>
  </div>
  <script>
    let numInput = document.querySelector('#numInput');
    let resultEle = document.querySelector('#result');
    let returnMax = (a, b) => a > b ? a : b;
    let calc = () => {
      let row = parseInt(numInput.value.split(' ')[0]) || null;
      let col = parseInt(numInput.value.split(' ')[1]) || null;
      if (!row || !col) return alert('请输入必填项');
      if (row < col) return alert('行必须大于列');
      let yanghuiMat = [];
      for (let a = 0; a <= returnMax(row, col); a++) {
        yanghuiMat[a] = [];
        for (let b = 0; b <= returnMax(row, col); b++) {
          if (a === b) {
            yanghuiMat[a][b] = 1
          } else if (a < b) {
            yanghuiMat[a][b] = 0
          } else {
            yanghuiMat[1][0] = 1
            yanghuiMat[a][0] = 1
            yanghuiMat[a][b] = yanghuiMat[a - 1][b - 1] + yanghuiMat[a - 1][b]
          }
        }
      }
      resultEle.innerText = yanghuiMat[row - 1][col - 1];
    }
  </script>
</body>

</html>

```