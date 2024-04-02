# Interview Questions-JavaScript

## Event Loop（事件循环）

2024.4.2

几个区域：调用栈（里面放正在执行的）、webAPI、微任务队列、宏任务队列

+ 事件循环具体顺序：

首先顺序执行语句，每个语句放进调用栈，执行完后出栈

对于进入调用栈的异步代码，交给异步进程（webAPI等），出栈；

webAPI中代码等待时机到回调队列中；

若同步任务全部执行完，轮询任务队列，并按照先微任务后宏任务的顺序进入调用栈执行。

重复以上过程，形成循环。

另：

+ 微任务执行时机比宏任务要早

- 微任务在DOM渲染前触发，宏任务在DOM渲染后触发



**宏任务**包括：setTimeout, setInterval, Ajax, DOM事件等

**微任务**包括：Promise, async/await等



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

执行步骤：

1. `console.log(1)`进入调用栈->打印1->出栈

2. `setTimeout()`进入调用栈->执行（将callBack(){}放入webAPI）->出栈
3. `new Promise()`进入调用栈->执行（将(resolve,reject)=>{}放入调用栈）
4. 执行`(resolve,reject)=>{}`（将`console.log(3)`放入调用栈）->打印3->出栈->将`resolve()`放入调用栈
5. 执行resolve()（将`then(res=>{})`放入webAPI）->出栈
6. 由于(resolve,reject)=>{}的部分执行完了，出栈
7. 由于new Promise()的部分执行完了，出栈
8. console.log(5)进栈->执行（打印5）->出栈
9. 由于现在所有同步代码执行完了，调用栈空了，开始执行微任务。

10. 此时刚好微任务then(res=>{})在微任务队列准备好了，进入调用栈，打印5，出栈；
11. 1秒后，callback从webAPI进入宏任务队列，进而进入调用栈，打印2，出栈。至此全部执行完成
