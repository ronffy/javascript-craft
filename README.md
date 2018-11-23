# javascript-craft
整理javascript的各种奇技淫巧

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

### 2. 异步请求的多次请求尝试

请求成功后，break语句会停止for循环；
请求失败后，catch捕获错误，进入下次循环。

```javascript
async function trySometimeRequest(time){
  for (let i = 0; i < time; i++) {
    try {
      await fetch('xxx');
      break;
    } catch (error) {}
  }
}
```

### 3. 一行代码实现简单模版引擎

```javascript
function template(tpl, data) {
  return tpl.replace(/{{(.*?)}}/g, (match, key) => data[key.trim()]);
}

// 使用：
template('我是{{name}}，年龄{{age}}，性别{{sex}}', {name: '王海荣', age: 18, sex: '男'}); 
// "我是王海荣，年龄18，性别男"
```

### 4. 数字转为千分位字符

```javascript
/**
 * 数字转为千分位字符
 * @param {Number} num
 * @param {Number} point 保留几位小数，默认2位
 */
function parseToThousandth(num, point = 2) {
  let [sInt, sFloat] = (Number.isInteger(num) ? `${num}` : num.toFixed(point)).split('.');
  sInt = sInt.replace(/\d(?=(\d{3})+$)/g, '$&,');
  return sFloat ? `${sInt}.${sFloat}` : `${sInt}`;
}
```

### 5. 带延时功能的链式调用

```javascript

// 1) 调用方式
new People('whr').sleep(3000).eat('apple').sleep(5000).eat('durian');

// 2) 打印结果
(等待3s)--> 'whr eat apple' -(等待5s)--> 'whr eat durian'

// 3) 以下是代码实现
class People {
  constructor(name) {
    this.name = name;
    this.queue = Promise.resolve();
  }
  eat(food) {
    this.queue = this.queue.then(() => {
      console.log(`${this.name} eat ${food}`)
    });
    return this;
  }
  sleep(time = 0) {
    this.queue = this.queue.then(() => new Promise(res => {
      setTimeout(() => {
        res();
      }, time);
    }))
    return this;
  }
}
```

### 6. 汇总各种异步请求的场景及解决方案

[汇总各种异步请求的场景及解决方案](https://github.com/ronffy/javascript-craft/blob/master/every-async.md)


### 7. 使用vscode编辑器，隐藏node_modules下_开头的文件夹，方便查看源码

点击设置，进入 User Settings （用户设置），添加如下配置：

```JSON
"files.exclude": {
  "**/node_modules/_**": true
}
```


### 8. 对象取值防错机制

#### 实现方式1

```javascript
function avoid(target, format) {
  if (!format || typeof format !== 'string') return target;
  let keys = format.split('.');
  let resault = target;
  for (const key of keys) {
    if (resault) {
      resault = resault[key];
    } else {
      return undefined;
    }
  }

  return resault;
}
```

#### 实现方式2（更符合取值理念，但修改原生对象的原型，谨慎使用）


```javascript
Object.prototype.getItem = function(format) {
  if (!format || typeof format !== 'string') return this;
  let keys = format.split('.');
  let resault = this;
  for (const key of keys) {
    if (resault) {
      resault = resault[key];
    } else {
      return undefined;
    }
  }

  return resault;
}
```

例如：
```javascript
let a = {}

let v1 = a.b.c.d; // 报错

let v2 = a && a.b && a.b.c && a.b.c.d; // 冗余代码太多

let v3 = avoid(a, 'b.c.d'); // 简便写法1

let v4 = a.getItem('b.c.d'); // 简便写法2
```


### 9. 用reduce实现的简版deepclone

```javascript

function deepclone(o) {
  const keys = Object.keys(o);
  return keys.reduce((memo, currKey) => {
    const value = o[currKey];
    if (value && typeof value === 'object') {
      return {
        ...memo,
        [currKey]: deepClone(value)
      }
    }
    return {
      ...memo,
      [currKey]: value
    }
  }, {})
}

```
