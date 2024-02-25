---
title: 【基础学习-html】9.html表单数据提交
categories: base
date: 2018-10-27 16:02:00
tags:
  - base
  - html
---
# iframe提交
```javascript
// 首先创建一个用来发送数据的iframe.
var iframe = document.createElement("iframe");
iframe.name = "myTarget";

// 必须把这个iframe插入当前文档.
window.addEventListener("load", function () {
  iframe.style.display = "none";
  document.body.appendChild(iframe);
});

// 下面这个函数是真正用来发送数据的.
// 它只有一个参数,一个包含键值对数据格式的对象.
function sendData(data) {
  var name,
      form = document.createElement("form"),
      node = document.createElement("input");

  // 注册iframe的load事件处理程序,如果你需要在响应返回时执行一些操作的话.
  iframe.addEventListener("load", function () {
    alert("Yeah! Data sent.");
  });
    
  form.action = "http://www.cs.tut.fi/cgi-bin/run/~jkorpela/echo.cgi";
  form.target = iframe.name;

  for(name in data) {
    node.name  = name;
    node.value = data[name].toString();
    form.appendChild(node.cloneNode());
  }

  // 表单元素需要添加到主文档中.
  form.style.display = "none";
  document.body.appendChild(form);

  form.submit();

  // 表单提交后,就可以删除这个表单,不影响下次的数据发送.
  document.body.removeChild(form);
}
```

# XMLHttpRequest
```javascript
function sendData(data) {
  var XHR = new XMLHttpRequest();
  var urlEncodedData = "";
  var urlEncodedDataPairs = [];
  var name;

  // 将数据对象转换为URL编码的键/值对数组。
  for(name in data) {
    urlEncodedDataPairs.push(encodeURIComponent(name) + '=' + encodeURIComponent(data[name]));
  }

  // 将配对合并为单个字符串，并将所有%-编码空间替换为
  // “+”字符；匹配浏览器窗体提交的行为。
  urlEncodedData = urlEncodedDataPairs.join('&').replace(/%20/g, '+');

  // 定义成功数据提交时发生的情况
  XHR.addEventListener('load', function(event) {
    alert('Yeah! Data sent and response loaded.');
  });

  // 定义错误提示
  XHR.addEventListener('error', function(event) {
    alert('哎呀！出了问题。');
  });

  // 建立我们的请求
  XHR.open('POST', 'https://example.com/cors.php');

  // 为表单数据POST请求添加所需的HTTP头
  XHR.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');

  // 最后，发送我们的数据。
  XHR.send(urlEncodedData);
}
```

# XMLHttpRequest 加 FormData 对象
```javascript
function sendData(data) {
  var XHR = new XMLHttpRequest();
  var FD  = new FormData();

  // 把我们的数据添加到这个FormData对象中
  for(name in data) {
    FD.append(name, data[name]);
  }

  // 定义数据成功发送并返回后执行的操作
  XHR.addEventListener('load', function(event) {
    alert('Yeah! Data sent and response loaded.');
  });

  // 定义发生错误时执行的操作
  XHR.addEventListener('error', function(event) {
    alert('Oups! Something goes wrong.');
  });

  // 设置请求地址和方法
  XHR.open('POST', 'http://ucommbieber.unl.edu/CORS/cors.php');

  // 发送这个formData对象,HTTP请求头会自动设置
  XHR.send(FD);
}
```

例子：
```html
<form id="myForm">
  <label for="myName">告诉我你的名字：</label>
  <input id="myName" name="name" value="John">
  <input type="submit" value="提交">
</form>
```
```javascript
window.addEventListener("load", function () {
  function sendData() {
    var XHR = new XMLHttpRequest();

    // 我们把这个 FormData 和表单元素绑定在一起。
    var FD  = new FormData(form);

    // 我们定义了数据成功发送时会发生的事。
    XHR.addEventListener("load", function(event) {
      alert(event.target.responseText);
    });

    // 我们定义了失败的情形下会发生的事
    XHR.addEventListener("error", function(event) {
      alert('哎呀！出了问题。');
    });

    // 我们设置了我们的请求
    XHR.open("POST", "http://ucommbieber.unl.edu/CORS/cors.php");

    // 发送的数据是由用户在表单中提供的
    XHR.send(FD);
  }
 
  // 我们需要获取表单元素
  var form = document.getElementById("myForm");

  // 取消表单的默认提交事件，用sendData代替
  form.addEventListener("submit", function (event) {
    event.preventDefault();

    sendData();
  });
});

```

# 发送二进制数据
使用append 方法将要发送的二进制数据添加进去。
```html
<form id="myForm">
  <p>
    <label for="i1">文本数据：</label>
    <input id="i1" name="myText" value="一些文本数据">
  </p>
  <p>
    <label for="i2">文件数据：</label>
    <input id="i2" name="myFile" type="file">
  </p>
  <button>提交</button>
</form>
```
上面是一个普通的表单,包含一个文件输入框,下面是要执行的JavaScript代码.
```javascript
// 因为我们想获取DOM节点,
// 我们在页面加载时初始化我们的脚本.
window.addEventListener('load', function () {

  // 这些变量用于存储表单数据
  var text = document.getElementById("i1");
  var file = {
        dom    : document.getElementById("i2"),
        binary : null
      };
 
  // 使用 FileReader API 获取文件内容
  var reader = new FileReader();

  // 因为 FileReader 是异步的, 会在完成读取文件时存储结果
  reader.addEventListener("load", function () {
    file.binary = reader.result;
  });

  // 页面加载时, 如果一个文件已经被选择, 那么读取该文件.
  if(file.dom.files[0]) {
    reader.readAsBinaryString(file.dom.files[0]);
  }

  // 如果没有，一旦用户选择了它，就读取文件。
  file.dom.addEventListener("change", function () {
    if(reader.readyState === FileReader.LOADING) {
      reader.abort();
    }
    
    reader.readAsBinaryString(file.dom.files[0]);
  });

  // 在我们的主函数中发送数据
  function sendData() {
    // 如果存在被选择的文件，等待它读取完成
    // 如果没有, 延迟函数的执行
    if(!file.binary && file.dom.files.length > 0) {
      setTimeout(sendData, 10);
      return;
    }

    // 要构建我们的多部分表单数据请求,
    // 我们需要一个XMLHttpRequest 实例
    var XHR = new XMLHttpRequest();

    // 我们需要一个分隔符来定义请求的每一部分。
    var boundary = "blob";

    // 将我们的请求主体存储于一个字符串中
    var data = "";

    // 所以，如果用户已经选择了一个文件
    if (file.dom.files[0]) {
      // 在请求体中开始新的一部分
      data += "--" + boundary + "\r\n";

      // 把它描述成表单数据
      data += 'content-disposition: form-data; '
      // 定义表单数据的名称
            + 'name="'         + file.dom.name          + '"; '
      // 提供文件的真实名字
            + 'filename="'     + file.dom.files[0].name + '"\r\n';
      // 和文件的MIME类型
      data += 'Content-Type: ' + file.dom.files[0].type + '\r\n';

      // 元数据和数据之间有一条空行。
      data += '\r\n';
      
      // 添加二进制数据到请求体中
      data += file.binary + '\r\n';
    }

    // 文本数据是简单的
    // 开始一个新的部分在请求体中
    data += "--" + boundary + "\r\n";

    // 说它是表单数据，并命名它
    data += 'content-disposition: form-data; name="' + text.name + '"\r\n';
    // 元数据和数据之间有一条空行。
    data += '\r\n';

    // 添加文本数据到请求体中
    data += text.value + "\r\n";

    // 一旦完成，关闭请求体
    data += "--" + boundary + "--";

    // 定义成功提交数据执行的语句
    XHR.addEventListener('load', function(event) {
      alert('✌！数据已发送且响应已加载。');
    });

    // 定义发生错误时做的事
    XHR.addEventListener('error', function(event) {
      alert('哎呀！出了问题。');
    });

    // 建立请求
    XHR.open('POST', 'https://example.com/cors.php');

    // 添加需要的HTTP报头来处理多部分表单数据POST请求
    XHR.setRequestHeader('Content-Type','multipart/form-data; boundary=' + boundary);

    // 最后，发送数据。
    XHR.send(data);
  }

  // 访问表单…
  var form = document.getElementById("myForm");

  // ……接管提交事件
  form.addEventListener('submit', function (event) {
    event.preventDefault();
    sendData();
  });
});
```