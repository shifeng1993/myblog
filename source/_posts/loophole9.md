---
title: 【查漏-js】9.常用合集和工具
date: 2019-01-18 14:07:00
tags:
  - base
  - js
---

# EventUtil——跨浏览器的事件对象
因为ie 的历史原因，与主流浏览器都有很多不兼容的地方

事件工具类是为了事件处理程序在跨多个浏览器上的兼容性。

```javascript
var EventUtil = {
  // 添加事件监听
  addHandler: function (element, type, handler) {
    if (element.addEventListener) {
      element.addEventListener(type, handler, false);
    } else if (element.attachEvent) {
      element.attachEvent("on" + type, handler);
    } else {
      element["on" + type] = handler;
    }
  },
  // 返回对 event 对象的引用 兼容ie
  getEvent: function (event) {
    return event ? event : window.event;
  },
  // 获取事件的目标
  getTarget: function (event) {
    return event.target || event.srcElement;
  },
  // 取消事件的默认行为
  preventDefault: function (event) {
    if (event.preventDefault) {
      event.preventDefault();
    } else {
      event.returnValue = false;
    }
  },
  // 移除事件监听
  removeHandler: function (element, type, handler) {
    if (element.removeEventListener) {
      element.removeEventListener(type, handler, false);
    } else if (element.detachEvent) {
      element.detachEvent("on" + type, handler);
    } else {
      element["on" + type] = null;
    }
  },
  // 跨浏览器阻止事件冒泡
  stopPropagation: function (event) {
    if (event.stopPropagation) {
      event.stopPropagation();
    } else {
      event.cancelBubble = true;
    }
  }
};


var btn = document.getElementById("myBtn");
var handler = function (event) {
  event = EventUtil.getEvent(event);
  EventUtil.preventDefault(event);
  EventUtil.stopPropagation(event);
};

EventUtil.addHandler(btn, "click", handler);
EventUtil.removeHandler(btn, "click", handler);
```

<style>
table {
  width: 100px;
}
</style>
# 键盘建码
## 主键盘字母和数字


| 按键        | 键码    |
| --------   | -----   |
| A        | 65      |
| B        | 66     |
| C        | 67      |
| D        | 68      |
| E        | 69      |
| F        | 70      |
| G        | 71     |
| H        | 72      |
| I        | 73      |
| J        | 74      |
| K        | 75      |
| L        | 76      |
| M        | 77     |
| N        | 78      |
| O        | 79      |
| P        | 80     |
| Q        | 81      |
| R        | 82      |
| S        | 83      |
| T        | 84      |
| U        | 85     |
| V        | 86      |
| W        | 87      |
| X        | 88      |
| Y        | 89      |
| Z        | 90      |
| 0        | 48     |
| 1        | 49      |
| 2        | 50     |
| 3        | 51     |
| 4        | 52      |
| 5        | 53      |
| 6        | 54      |
| 7        | 55      |
| 8        | 56     |
| 9        | 57     |

## 数字小键盘和功能键盘
| 按键        | 键码    |
| --------   | -----   |
| 0        | 96     |
| 1        | 97     |
| 2        | 98     |
| 3        | 99     |
| 4        | 100      |
| 5        | 101      |
| 6        | 102      |
| 7        | 103      |
| 8        | 104     |
| 9        | 105     |
| *        | 106     |
| +        | 107     |
| Enter    | 108     |
| -        | 109     |
| .        | 110      |
| /        | 111      |
| F1        | 112      |
| F2        | 113      |
| F3        | 114     |
| F4        | 115     |
| F5        | 116      |
| F6        | 117      |
| F7        | 118     |
| F8        | 119     |
| F9        | 120      |
| F10        | 121      |
| F11        | 122     |
| F12        | 123     |

## 控制键

| 按键        | 键码    |
| --------   | -----   |
| BackSpace | 8 |
| Esc | 27 |
| Right Arrow | 39 |
| Left Arrow | 37 |
| Down Arrow | 40 |
| Up Arrow | 38 |
| -_  | 189 |
| .&gt;  | 190 |
| Spacebar  | 32 |
| Tab  | 9 |
| Clear  | 12 |
| Page Up  | 33  |
| Page Down  | 34 |
| Enter  | 13 |
| Insert  | 45 |
| ;:  | 186 |
| Delete  | 46 |
| `~  | 192 |
| /? | 191 |
| Num Lock  | 144 |
| Control  | 17 |
| Home  | 36 |
| End  | 35 |
| Shift  | 16 |
| [{  | 219 |
| }]  | 221 |
| \   | 220 |
| =+  | 187 |
| ,&lt;  | 188 |
| '"  | 222  |
| Cape Lock  | 20 |
| Alt  | 18 |
