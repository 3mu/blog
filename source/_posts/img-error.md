---
title: img error事件冒泡
date: 2016-12-28 09:48:22
tags: javascript
---

```javascript
 document.addEventListener("error", function (e) {
        var $img = $(e.target);
        var src = $img.attr("src");
        //to do
    }, true);
```
