# Interview Questions-JavaScript

## Event Loop（事件循环）

2024.4.2

几个区域：调用栈（里面放正在执行的）、webAPI、callback queue（回调队列）

首先顺序执行语句，每个语句放进调用栈，执行完后出栈

对于进入调用栈的异步代码，放入webAPI中，出栈；webAPI中代码等待时机到回调队列中，调用栈空时eventLoop开始工作，轮询

另：

+ 微任务执行时机比宏任务要早

- 微任务在DOM渲染前触发，宏任务在DOM渲染后触发



### 事件循环整体流程：

1. 先清空call stack中的同步代码
2. 执行微任务队列中的微任务
3. 尝试DOM渲染
4. 触发Event Loop反复询问callbackQueue中是否有要执行的语句，有则放入call back继续执行



### 例子：

```javascript
console.log(1);
setTimeout(function callback(){
    console.log(2);
},1000);
new Promise((resolve,reject)=>{
    console.log(3);
    resolve()
}).then(res=>{
    console.log(4);
})
console.log(5)
```

