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



## 细节语法(ES6及之前)

### Object

#### Object.defineProperty()

**语法：**

```javascript
Object.defineProperty(obj, prop, descriptor)
```

- **obj**：要在其上定义属性的对象。
- **prop**：要定义或修改的属性的名称。
- **descriptor**：要定义或修改的属性描述符。




**属性描述符descriptor：**

属性描述符是一个对象，它描述了该属性的配置。它有两种形式：数据描述符和访问器描述符。

- **数据描述符**：它具有以下可选键：
  - `value`：属性的值。
  - `writable`：当且仅当属性的值可以被改变时为`true`。
  - `configurable`：若为`true`，该属性的描述符可以被改变，同时该属性也可以从对应的对象上被删除。
  - `enumerable`：若为`true`，该属性会出现在对象的枚举属性中。
- **访问器描述符**：它具有以下可选键：
  - `get`：一个给属性提供getter的方法，如果没有getter则为`undefined`。
  - `set`：一个给属性提供setter的方法，如果没有setter则为`undefined`。
  - `configurable`和`enumerable`与数据描述符中的相同。

==数据描述符和访问器描述符不能混合使用==。也就是说，如果一个属性描述符有`value`或`writable`键，那么它就不能有`get`或`set`键，反之亦然。

**例子：两种写法给一个Object加属性并改名**

+ **写法一：**

```javascript
let obj = {};
Object.defineProperty(obj, 'property1', {
    value:42,
    writable:true
});
console.log(obj);
console.log(obj.property1);
obj.property1=10;
console.log(obj.property1);
```

注：似乎用defineProperty声明的obj的property1和直接用let obj={property1:10}有少量区别，前者的可配置性、扩展性更强些。

+ **写法二：**

```javascript
let obj = {
    _property1: "Initial"
};
Object.defineProperty(obj, 'property1', {
    set(value){
        this._property1=value;
    },
    get(){
        return this._property1;
    }
});
console.log(obj);
console.log(obj.property1);
obj.property1="changed";
console.log(obj.property1);
```

也可以写作不用defineProperty的写法，直接把get和set放在对象里声明：

```javascript
let obj = {
    _property1: "Initial",
    set property1(value){
        this._property1=value;
    },
    get property1(){
        return this._property1
    }
};
console.log(obj,obj.property1);
obj.property1="changed";
console.log(obj.property1)
```

+ **使用defineProperty的优点（以下是GPT原文）：**

通过定义带有getter和setter的访问器属性，可以提供更灵活的属性访问和修改方式，相比之下，直接对原属性进行修改和访问则更加直接且缺乏灵活性。

**比较如下：**

+ **直接对原属性进行修改和访问**

```
let obj = {
  _value: 0,
  getValue() {
    return this._value;
  },
  setValue(value) {
    this._value = value;
  }
};

console.log(obj.getValue()); // 输出：0
obj.setValue(10);
console.log(obj.getValue()); // 输出：10
```

这个例子中，我们直接通过`getValue`和`setValue`方法来获取和修改对象的`_value`属性。这种方式需要显式地调用方法来获取和设置属性的值。

+ **定义带有getter和setter的访问器属性**

```
let obj = {
  _value: 0,
  get value() {
    return this._value;
  },
  set value(value) {
    this._value = value;
  }
};

console.log(obj.value); // 输出：0
obj.value = 10;
console.log(obj.value); // 输出：10
```

在这个例子中，我们使用了ES5引入的访问器属性的写法，通过`get`和`set`关键字来定义属性的getter和setter方法。这种方式使得我们可以像访问普通属性一样去获取和设置属性的值，而背后的getter和setter方法会自动地被调用。
