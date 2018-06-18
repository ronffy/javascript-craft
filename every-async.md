# 汇总各种异步请求的场景及解决方案

## 单个异步

## 用generator实现async

### 如何使用
除了generator和async的语法区别外，使用区别是在generator函数外包一个execut自执行函数

```javascript
execut(function* g(){
  let data1 = yield fetch(/* */);
  let data2 = yield fetch(/* */);
  // ...
})
```

### 如何实现execut自执行函数
```javascript
function execut(genFn){
  return new Promise((res, rej) => {
    let gen = genFn();
    const step = genNext => {
		  let next;
      try{
        next = genNext();
      }catch(e){
        return rej(e);
      }
      if(next.done)return res(next.value);
      Promise.resolve(next.value).then(value => {
        // 执行generator的next方法
        step(() => gen.next(value));
      }).catch(e => {
        // 将错误抛出给外界使用者
        step(() => gen.throw(e));
      })
    };
    step(() => gen.next());
  })
}
```

## 多个异步



1.有依赖关系

2.没有依赖关系