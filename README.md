# javascript-craft
收集javascript的各种奇技淫巧

### 1. compose合并函数依次执行 - 来源redux

```javascript
function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```
### 2.从浏览器输入网址到页面渲染完成，发生了什么事情？

-> 浏览器解析
-> 查询缓存
-> dns查询
-> 建立链接
-> 服务器处理请求
-> 服务器发送响应
-> 客户端收到页面
-> 解析HTML
-> 构建渲染树
-> 开始显示内容(白屏时间)
-> 首屏内容加载完成(首屏时间)
-> 用户可交互(DOMContentLoaded)
-> 加载完成(load)
