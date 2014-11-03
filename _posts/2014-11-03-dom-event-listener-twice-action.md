---
layout: post
title: DOM事件的两次触发
---

# DOM事件的两次触发

根据DOM规范，一个EventListener对象是由(type, callback, capture)三元组唯一标识的。

这会导致一个奇怪的问题，下面的代码document会响应两次事件

``` html
<html>
  <head>
  </head>
  <body>
    <script language="javascript">
      function test(ev) {
        alert(ev.target, ev.currentTarget, ev.eventPhase);
      }

      document.addEventListener("hey", test, true);
      document.addEventListener("hey", test, false);

      var ev = new Event("hey");

      document.dispatchEvent(ev);
    </script>
  </body>
</html>
```

不知道是委员会有意为之，还是DOM的缺陷。
