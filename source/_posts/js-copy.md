---
title: js-copy
date: 2022-10-10 11:43:08
tags: js基础
---


### 原生js实现复制

```js
 function onCopy(text) {
   const input = document.createElement('input');
   input.value = text
   document.body.appendChild(input)
   input.select()
   document.execCommand('Copy') 
   input.remove()
 }
```