## JavaScript 进阶 

> 学习作用域、变量提升、闭包等语言特征，加深对 JavaScript 的理解，掌握变量赋值、函数声明的简洁语法，降低代码的冗余度。

- 理解作用域对程序执行的影响
- 能够分析程序执行的作用域范围
- 理解闭包本质，利用闭包创建隔离作用域
- 了解什么变量提升及函数提升
- 掌握箭头函数、解析剩余参数等简洁语法

### 一、作用域

> 目标：了解作用域对程序执行的影响及作用域链的查找机制，使用闭包函数创建隔离作用域避免全局变量污染。

作用域（scope）规定了变量能够被访问的“范围”，离开了这个“范围”变量便不能被访问，作用域分为全局作用域和局部作用域。

#### 1.1 局部作用域

> 局部作用域分为函数作用域  和  块作用域。
>

##### 函数作用域

在函数内部声明的变量只能在函数内部被访问，外部无法直接访问。

```html
<script>
  // 声明 counter 函数
  function counter(x, y) {
    // 函数内部声明的变量
    const s = x + y  //s是局部变量
    console.log(s) // 18
  }
  // 设用 counter 函数
  counter(10, 8)
  // 访问变量 s
  console.log(s)// 报错  函数外部不能使用局部作用域变量
</script>
```

总结：

1. 函数内部声明的变量，在函数外部无法被访问
2. 函数的参数也是函数内部的局部变量
3. 不同函数内部声明的变量无法互相访问
4. 函数执行完毕后，函数内部的变量实际被清空了

##### 块作用域

在 JavaScript 中使用 `{}` 包裹的代码称为代码块，代码块内部声明的变量外部将【有可能】无法被访问。

```html
<script>
  {
    // age 只能在该代码块中被访问
    let age = 18;
    console.log(age); // 正常
  }
  
  // 超出了 age 的作用域
  console.log(age) // 报错
  
  let flag = true;
  if(flag) {
    // str 只能在该代码块中被访问
    let str = 'hello world!'
    console.log(str); // 正常
  }
  
  // 超出了 age 的作用域
  console.log(str); // 报错
  
  for(let t = 1; t <= 6; t++) {
    // t 只能在该代码块中被访问
    console.log(t); // 正常
  }
  
  // 超出了 t 的作用域
  console.log(t); // 报错
</script>
```

JavaScript 中除了变量外还有常量，常量与变量本质的区别是【常量必须要有值且不允许被重新赋值】，常量值为对象时其属性和方法允许重新赋值。

```html
<script>
  // 必须要有值
  const version = '1.0.0';

  // 不能重新赋值
  // version = '1.0.1';

  // 常量值为对象类型
  const user = {
    name: '小明',
    age: 18
  }

  // 不能重新赋值
  user = {}

  // 属性和方法允许被修改
  user.name = '小小明'
  user.gender = '男'
</script>
```

总结：

1. `let` 声明的变量会产生块作用域，`var` 不会产生块作用域
2. `const` 声明的常量也会产生块作用域
3. 不同代码块之间的变量无法互相访问
4. 推荐使用 `let` 或 `const`

注：开发中 `let` 和 `const` 经常不加区分的使用，如果担心某个值会不小被修改时，则只能使用 `const` 声明成常量。

#### 1.2 全局作用域

`<script>` 标签和 `.js` 文件的【最外层】就是所谓的全局作用域，在此声明的变量在函数内部也可以被访问。

```html
<script>
  // 此处是全局
  
  function sayHi() {
    // 此处为局部
  }

  // 此处为全局
</script>
```

全局作用域中声明的变量，任何其它作用域都可以被访问，如下代码所示：

```html
<script>
    // 全局变量 name
    const name = '小明'
  
  	// 函数作用域中访问全局
    function sayHi() {
      // 此处为局部
      console.log('你好' + name)
    }

    // 全局变量 flag 和 x
    const flag = true
    let x = 10
  
  	// 块作用域中访问全局
    if(flag) {
      let y = 5
      console.log(x + y) // x 是全局的
    }
</script>
```

注意：

1. 为 `window` 对象动态添加的属性默认也是全局的，不推荐！
2. 函数中未使用任何关键字声明的变量为全局变量，不推荐！！！
3. 尽可能少的声明全局变量，防止全局变量被污染

JavaScript 中的作用域是程序被执行时的底层机制，了解这一机制有助于规范代码书写习惯，避免因作用域导致的语法错误。  



#### 1.3 作用域链

在解释什么是作用域链前先来看一段代码：

```html
<script>
  // 全局作用域
  let a = 1
  let b = 2
  // 局部作用域
  function f() {
    let c
    // 局部作用域
    function g() {
      let d = 'yo'
    }
  }
</script>
```

函数内部允许创建新的函数，`f` 函数内部创建的新函数 `g`，会产生新的函数作用域，由此可知作用域产生了嵌套的关系。



作用域链本质上是底层的**变量查找机制**，在函数被执行时，会**优先查找当前**函数作用域中查找变量，如果当前作用域查找不到则会**依次逐级查找父级作用域**直到全局作用域，如下代码所示：

```html
<script>
  // 全局作用域
  let a = 1
  let b = 2

  // 局部作用域
  function f() {
    let c
    // let a = 10;
    console.log(a) // 1 或 10
    console.log(d) // 报错
    
    // 局部作用域
    function g() {
      let d = 'yo'
      // let b = 20;
      console.log(b) // 2 或 20
    }
    
    // 调用 g 函数
    g()
  }

  console.log(c) // 报错
  console.log(d) // 报错
  
  f();
</script>
```

总结：

1. 嵌套关系的作用域串联起来形成了作用域链
2. 相同作用域链中按着从小到大的规则查找变量
3. 子作用域能够访问父作用域，父级作用域无法访问子级作用域



#### 1.31 `js`垃圾回收机制

1. 什么是垃圾回收机制？

      垃圾回收机制(Garbage Collection) 。`js`中内存的分配和回收都是自动完成的，内存在不使用的时候会被垃圾回收器自动回收。正因为垃圾回收器的存在，许多人认为`js`不用太关心内存管理的问题但如果不了解`js`的内存管理机制，我们同样非常容易成内存泄漏（内存无法被回收）的情况不再用到的内存，没有及时释放，就叫做内存泄漏。

2. 内存的生命周期

   `js`环境中分配的内存, 一般有如下生命周期：

   1. 内存分配：当我们声明变量、函数、对象的时候，系统会自动为他们分配内存

   2. 内存使用：即读写内存，也就是使用变量、函数等

   3. 内存回收：使用完毕，由垃圾回收自动回收不再使用的内存

   4. 说明：

    全局变量一般不会回收(关闭页面回收)

    一般情况下局部变量的值, 不用了, 会被自动回收掉

3. 垃圾回收算法说明

   所谓垃圾回收, 核心思想就是如何判断内存是否已经不再会被使用了, 如果是, 就视为垃圾, 释放掉

   下面介绍两种常见的浏览器垃圾回收算法: **引用计数法** 和 **标记清除法**

#### 1.4 闭包

- 概念：一个函数对周围状态的引用捆绑在一起，内层函数中访问到其外层函数的作用域。
- 简单理解：**闭包 = 内层函数 + 外层函数的变量**
- 闭包作用：封闭数据，提供操作，外部也可以访问函数内部的变量。
- 闭包应用：*实现数据的私有*。比如我们要做个统计函数调用次数，函数调用一次，就++

闭包是一种比较特殊和函数，使用闭包能够访问函数作用域中的变量。从代码形式上看闭包是一个做为返回值的函数，如下代码所示：

```html
<script>
    //闭包的基本格式
  function foo() {
    let i = 0;

    // 函数内部分函数
    function bar() {
			console.log(++i);
    }

    // 将函数做为返回值
    return bar    //foo（）是接收者
  }
  
  // fn 即为闭包函数 fn装的是函数
  let fn = foo();
  
  fn(); // 1  //调用函数
    
    //外层函数使用内层函数的变量
    //foo() === bar === function bar(){ }
    //let fn = function(){ }
    //fn装的是函数   调用fn()
</script>
```

总结：

1. 闭包本质仍是函数，只不是从函数内部返回的
2. 闭包能够创建外部可访问的隔离作用域，避免全局变量污染
3. 过度使用闭包可能造成**内存泄漏**

注：回调函数也能访问函数内部的局部变量。

注：闭包很有用，因为它允许将函数与其所操作的某些数据(环境)关联起来。闭包可能引起的问题：内存泄漏



#### 1.5 变量提升

变量提升是 JavaScript 中比较“奇怪”的现象，它允许在变量声明之前即被访问

（仅存在于var声明的变量！）

```html
<script>
  // 访问变量 str
  console.log(str + 'world!');

  // 声明变量 str
  var str = 'hello ';
</script>
```

```javascript
var num
console.log(num) //undefined
num = 10

//变量提升是什么流程？
//1.先把var 变量提升到当前作用域的最前面
//2.只提升变量声明，不提升变量赋值。
//3.然后依次执行代码。
```

总结：

1. 变量在未声明即被访问时会报语法错误
2. 变量在var声明之前即被访问，变量的值为 `undefined`
3. `let`/`const`声明的变量不存在变量提升，推荐使用 `let`
4. 变量提升出现在相同作用域当中
5. **实际开发中推荐先声明再访问变量**

注：关于变量提升的原理分析会涉及较为复杂的词法分析等知识，而开发中使用 `let` 可以轻松规避变量的提升，因此在此不做过多的探讨，有兴趣可[查阅资料](https://segmentfault.com/a/1190000013915935)。



### 二、函数

> 目标：知道函数参数默认值、动态参数、剩余参数的使用细节，提升函数应用的灵活度，知道箭头函数的语法及与普通函数的差异。

#### 2.1 函数提升

函数提升与变量提升比较类似，是指函数在声明之前即可被调用。

```html
<script>
  // 调用函数
  foo()
  // 声明函数
  function foo() {
    console.log('声明之前即被调用...')
  }

  // 不存在提升现象
  bar()  // 错误
  var bar = function () {
    console.log('函数表达式不存在提升现象...')  //不是在声明，是在赋值给fun
  }
</script>
```

总结：

1. 函数提升能够使函数的声明调用更灵活
2. **函数表达式不存在提升的现象**
3. 函数提升出现在相同作用域当中

#### 2.2 参数

函数参数的使用细节，能够提升函数应用的灵活度。

##### 默认值

```html
<script>
  // 设置参数默认值
  function sayHi(name="小明", age=18) {
    document.write(`<p>大家好，我叫${name}，我今年${age}岁了。</p>`);
  }
  // 调用函数
  sayHi();
  sayHi('小红');
  sayHi('小刚', 21);
</script>
```

总结：

1. 声明函数时为形参赋值即为参数的默认值
2. 如果参数未自定义默认值时，参数的默认值为 `undefined`
3. 调用函数时没有传入对应实参时，参数的默认值被当做实参传入

##### 动态参数

`arguments` 是函数内部内置的 伪数组  变量，它包含了调用函数时传入的所有实参。

当不确定传递多少个实参的时候，用`arguments`动态参数

```html
<script>
  // 求生函数，计算所有参数的和
  function sum() {
    // console.log(arguments)
    let s = 0
    for(let i = 0; i < arguments.length; i++) {
      s += arguments[i]
    }
    console.log(s)
  }
  // 调用求和函数
  sum(5, 10)// 两个参数
  sum(1, 2, 4) // 两个参数
</script>
```

总结：

1. `arguments` 是一个伪数组，只存在于函数中
2. `arguments` 的作用是动态获取函数的实参
3. 可以通过for 循环依次得到传递过来的实参



##### 剩余参数

剩余参数允许我们将一个不定数量的参数表示为一个 数组

```javascript
function getSum(...other){
    //other得到[1,2,3] 真数组
    console.log(other)
}
getSum(1,2,3)
```

```html
<script>
  function config(baseURL, ...other) {   //...other用于获取多余的实参！ other可以随便取名。
    console.log(baseURL) // 得到 'http://baidu.com'
    console.log(other)  // other  得到 ['get', 'json']
  }
  // 调用函数
  config('http://baidu.com', 'get', 'json');
</script>
```

总结：

1. `...` 是语法符号，置于最末函数形参之前，用于获取多余的实参
2. 借助 `...` 获取的剩余实参，是个真数组
3. 开发中，还是提倡多使用 **剩余参数**

区别：

1. 动态参数是为数组
2. 剩余参数是真数组



##### 展开运算符

```javascript
//目标：能够使用展开运算符说出常用的使用场景
//展开运算符（...）,将一个数组进行展开。
       const arr = [1,5,3,8,2]
       console.log(...arr)  // 1 5 3 8 2

//说明：不会修改原数组

//典型运用场景：求数组最大值最小值，合并数组等。
       const arr = [1,5,3,8,2]
       console.log(Math.max(...arr)) //18
       console.log(Math.min(...arr)) //1
      
        //合并数组
       const arr1 = [1,2]
       const arr2 = [3,4]
       const arr3 = [...arr1,...arr2]
       console.log(arr3)   //[1,2,3,4]

//展开运算符or剩余参数
//剩余参数：函数参数使用，得到真数组
//展开运算符：数组中使用，数组展开

      console.log(Math.max(arr))  //NaN
      console.log(Math.max(1,2,3)) //3
      //...arr === 1,2,3    console.log(Math.max(...arr))
```



#### 2.3 箭头函数

- 箭头函数是一种声明函数的简洁语法，它与普通函数并无本质的区别，差异性更多体现在语法格式上。

- 目的：引入箭头函数的目的是更简洁的函数写法并且不绑定this，箭头函数的语法比函数表达式更简洁。

- 使用场景：箭头函数更适用于那些本来 需要匿名函数的地方。

```javascript
     //普通函数
     const fn = function () {
      console.log(123)
     }
     
     //1. 箭头函数 基本语法
     const fn = () => {
       console.log(123)
     }
     fn()
     const fn = (x) => {
       console.log(x)
     }
     fn(1)

    // 2. 只有一个形参的时候，可以省略小括号
     const fn = x => {
       console.log(x)
     }
     fn(1)

     // 3. 只有一行代码的时候，我们可以省略大括号
     const fn = x => console.log(x)
     fn(1)

    // 4. 只有一行代码的时候，可以省略return
     const fn = x => x + x
     console.log(fn(1))

    // 5. 箭头函数可以直接返回一个对象
    //加括号的函数体返回对象字面量表达式。
     const fn = (uname) => ({ uname: uname })
     console.log(fn('刘德华'))

```

```html
<script>
  // 箭头函数
  const foo = () => {
    console.log('^_^ 长相奇怪的函数...');
  }
  // 调用函数
  foo()
  
  // 更简洁的语法
  const form = document.querySelector('form')
  form.addEventListener('click', ev => ev.preventDefault())
</script>
```

总结：

1. 箭头函数属于表达式函数，因此不存在函数提升
2. 箭头函数只有一个参数时可以省略圆括号 `()`
3. 箭头函数函数体只有一行代码时可以省略花括号 `{}`，并自动做为返回值被返回
4. 箭头函数中没有 `arguments`，只能使用 `...` 动态获取实参
5. 加括号的函数体返回对象字面量表达式



##### 箭头函数参数

1. 普通函数有`arguments`动态参数。
2. 箭头函数没有`arguments`动态参数，但是有 剩余参数  `...args`

```javascript
 //  利用箭头函数来求和
    const getSum = (...arr) => {   //剩余参数 ...arr 可以随意换名字
      let sum = 0
      for (let i = 0; i < arr.length; i++) {
        sum += arr[i]
      }
      return sum
    }
    const result = getSum(2, 3, 4)
    console.log(result) // 9
```



##### 箭头函数this指向

- 箭头函数不会创建自己的this，它只会从自己的作用域链的上一层沿用this。

- 事件回调函数使用箭头函数时，this 为全局的 window , 因此Dom事件回调函数为了简便，还是不太推荐使用箭头函数。

  ```javascript
  // 以前this的指向：  谁调用的这个函数，this 就指向谁
       console.log(this)  // window
       // 1.普通函数
       function fn() {
         console.log(this)  // window 普通函数指向调用者
       }
       fn()  // window.fn()
       btn.addEventListener('click',function() {
           console.log(this)  // 当前 this 指向 btn
       })
       // 对象方法里面的this
       const obj = {
         name: 'andy',
         sayHi: function () {
           console.log(this)  // obj
         }
       }
       obj.sayHi()  //普通函数指向调用者
  
  
      // 2. 箭头函数的this  是上一层作用域的this 指向
       const fn = () => {
         console.log(this)  // window 上一层是全局
       }
       fn()
      // 对象方法箭头函数 this
       const obj = {
         uname: 'pink老师',
         sayHi: () => {
           console.log(this)  // this 指向谁？ window
         }
       }
       obj.sayHi()  //window.obj.sayHi()
  
      const obj = {
        uname: 'pink老师',
        sayHi: function () {
          console.log(this)  // obj
          let i = 10
          const count = () => {
            console.log(this)  // obj //沿用上一层
          }
          count()
        }
    }
      obj.sayHi()
  ```
  
  

### 三、`ES6`-解构赋值

> 知道解构的语法及分类，使用解构简洁语法快速为变量赋值。

解构赋值是一种快速为变量赋值的简洁语法，本质上仍然是为变量赋值，分为数组解构、对象解构两大类型。

#### 3.1 数组解构

数组解构是将数组的 单元值 快速 批量 赋值 给一系列变量的简洁语法，如下代码所示：

```js
<script>
  // 普通的数组
  let arr = [1, 2, 3];
  // 批量声明变量 a b c 
  // 同时将数组单元值 1 2 3 依次赋值给变量 a b c
  let [a, b, c] = arr;
  console.log(a); // 1
  console.log(b); // 2
  console.log(c); // 3
</script>

     //基本语法：典型应用交互两个变量
     let a = 1
     let b = 3
     ;[b,a] = [a,b]     //这里必须加分号，隔开！
     console.log(a)  //3
     console.log(b)  //1
```

总结：

1. 赋值运算符 `=` 左侧的 `[]` 用于批量声明变量，右侧数组的单元值将被赋值给左侧的变量

2. 变量的顺序对应数组单元值的位置依次进行赋值操作

   ```javascript
        const pc = ['海尔', '联想', '小米', '方正']
        [hr, lx, mi, fz] = pc
        console.log(hr, lx, mi, fz)
   
        const pc = ['海尔', '联想', '小米', '方正']
        const [hr, lx, mi, fz] = ['海尔', '联想', '小米', '方正']
        console.log(hr)
        console.log(lx)
        console.log(mi)
        console.log(fz)
   
        // 请将最大值和最小值函数返回值解构 max 和min 两个变量
        function getValue() {
          return [100, 60]
        }
        const [max, min] = getValue()
        console.log(max)
        console.log(min)
   ```

3. 变量的数量大于单元值数量时，多余的变量将被赋值为  `undefined`

   ```js
         // 变量多， 单元值少 ， undefined
        const [a, b, c, d] = [1, 2, 3]
        console.log(a) // 1
        console.log(b) // 2
        console.log(c) // 3
        console.log(d) // undefined
   ```

4. 变量的数量小于单元值数量时，可以通过 `...` 获取剩余单元值，但只能置于最末位

   ```js
         // 2. 变量少， 单元值多
        const [a, b] = [1, 2, 3]
        console.log(a) // 1
        console.log(b) // 2
   
        // 3.  利用剩余参数 变量少， 单元值多的情况
        const [a, b, ...c] = [1, 2, 3, 4]
        console.log(a) // 1
        console.log(b) // 2
        console.log(c) // [3, 4]  真数组
   ```

5. 允许初始化变量的默认值，且只有单元值为 `undefined` 时默认值才会生效

   ```js
   // 4.  防止 undefined 传递单元值的情况，可以设置默认值
        const [a = 0, b = 0] = [1, 2]
       // const [a = 0, b = 0] = []
        console.log(a) // 1
        console.log(b) // 2
   
       // 5.  按需导入赋值，忽略某些值
        const [a, b, , d] = [1, 2, 3, 4]
        console.log(a) // 1
        console.log(b) // 2
        console.log(d) // 4
   
   ```

注：支持多维解构赋值，比较复杂后续有应用需求时再进一步分析

```js
     const arr = [1, 2, [3, 4]]
     console.log(arr[0])  // 1
     console.log(arr[1])  // 2
     console.log(arr[2])  // [3,4]
     console.log(arr[2][0])  // 3

    // 多维数组解构
     const arr = [1, 2, [3, 4]]
     const [a, b, c] = [1, 2, [3, 4]]
     console.log(a) // 1
     console.log(b) // 2
     console.log(c) // [3,4]


    const [a, b, [c, d]] = [1, 2, [3, 4]]
    console.log(a) // 1
    console.log(b) // 2
    console.log(c) // 3
    console.log(d) // 4
```



#### 3.2 对象解构

对象解构是将对象属性和方法快速批量赋值给一系列变量的简洁语法，如下代码所示：

```html
<script>
  // 普通对象
  const user = {
    name: '小明',
    age: 18
  };
  // 批量声明变量 name age
  // 同时将数组单元值 小明  18 依次赋值给变量 name  age
  const {name, age} = user

  console.log(name) // 小明
  console.log(age) // 18
</script>
```

```js
     // 普通对象
     const obj = {
       uname: 'pink老师',
       age: 18
     }
    // 解构的语法 批量声明变量 uname age  同时将单元值依次赋值给变量uname age
     const { uname, age } = obj
     // 要求属性名和变量名必须一直才可以
     console.log(uname) //pink老师
     console.log(age) //18

    //给新的变量名赋值
    //对象解构的变量名 可以重新改名  旧变量名: 新变量名
    //可以从一个对象中提取变量并同时修改新的变量名。
     const { uname: username, age } = obj  //把原来的uname变量名重新命名为 usernamme
     console.log(username) //pink老师
     console.log(age) //18


   // 2. 解构数组对象
    const pig = [
      {
        uname: '佩奇',
        age: 6
      }
    ]
    const [{ uname, age }] = pig
    console.log(uname) //佩奇
    console.log(age)  //6

  //3.多级对象解构
    const person = [
      {
        name: '佩奇',
        family: {
          mother: '猪妈妈',
          father: '猪爸爸',
          sister: '乔治'
        },
        age: 6
      }
    ]
    //多级对象解构
    const [{ name, family: { mother, father, sister } }] = person
    console.log(name)
    console.log(mother)
    console.log(father)
    console.log(sister)
```

总结：

1. 赋值运算符 `=` 左侧的 `{}` 用于批量声明变量，右侧对象的属性值将被赋值给左侧的变量
2. 对象属性的值将被赋值给与属性名相同的变量
3. 对象中找不到与变量名一致的属性时变量值为 `undefined`
4. 允许初始化变量的默认值，属性不存在或单元值为 `undefined` 时默认值才会生效
5. 注意解构的变量名不要和外面的变量名冲突否则会报错

注：支持多维解构赋值，比较复杂后续有应用需求时再进一步分析



### `js`前面必须加分号的情况

```js
//1.立即执行函数
(function fn() { })();
;(function fn() { })()  //分号必须加

//2.数组解构
  //数组开头的，特别是前面有语句的一定注意加分号
;[b,a] = [a,b]
```



### `forEach`方法

- `forEach` 方法用于调用数组的每个元素，并将元素传递给回调函数

- 主要使用场景： 遍历数组的每个元素

- 语法：

  ```js
  //语法
  被遍历的数组.forEach(function(当前数组元素，当前元素索引号) {
      //函数体                     item         i
  })
  
  // forEach 就是遍历  加强版的for循环  适合于遍历数组对象
      const arr = ['red', 'green', 'pink']
      const result = arr.forEach(function (item, index) {
        console.log(item)  // 数组元素 red  green pink
        console.log(index) // 索引号 依次打印每一个元素的索引号
      })
      // console.log(result)
  ```

注意：

1. `forEach`主要是遍历数组
2. 参数当前数组元素item 是必须要写的，索引号可选。



### `filter()`方法

- filter() 方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素

- 主要使用场景： 筛选数组符合条件的元素，并返回筛选之后元素的新数组

- 语法：

  ```js
  //语法
  被遍历的数组.filter(function(currentValue,index) {
      return 筛选条件
  })
  //currentValue 值
  //index 索引号
   
        //filter()筛选数组大于20的数
       const arr = [10, 20, 30]
       const newArr = arr.filter(function (item, index) {
         // console.log(item)
         // console.log(index)
         return item >= 20
       })
      // 返回的符合条件的新数组
  
     // const newArr = arr.filter(item => item >= 20)
      console.log(newArr)
  ```

注：

1. filter()  筛选数组
2. 返回值：返回数组，包含了符合条件的所有元素。如果没有符合条件的元素则返回空数组
3. 参数： `currentValue` 必须写， index 可选
4. 因为返回新数组，所以不会影响原数组

```js
      //数组去重
const a = function(arr) {
    let newArr = arr.filter((item,index)=> {
        return arr.indexOf(item) === index
    })
    console.log(newArr)
}
a([1,2,2,3])
```



### 综合案例-渲染商品案例

![image-20230123200258069](assets\image-20230123200258069.png)

```js
<body>
    <div class="filter">
        <a data-index="1" href="javascript:;">0-100元</a>
        <a data-index="2" href="javascript:;">100-300元</a>
        <a data-index="3" href="javascript:;">300元以上</a>
        <a href="javascript:;">全部区间</a>
    </div>
    <div class="list">
        <!-- <div class="item">
      <img src="" alt="">
      <p class="name"></p>
      <p class="price"></p>
    </div> -->
    </div>
    <script>
        // 2. 初始化数据
        //后台接受过来的数据
        const goodsList = [{
            id: '4001172',
            name: '称心如意手摇咖啡磨豆机咖啡豆研磨机',
            price: '289.00',
            picture: 'https://yanxuan-item.nosdn.127.net/84a59ff9c58a77032564e61f716846d6.jpg',
        }, {
            id: '4001594',
            name: '日式黑陶功夫茶组双侧把茶具礼盒装',
            price: '288.00',
            picture: 'https://yanxuan-item.nosdn.127.net/3346b7b92f9563c7a7e24c7ead883f18.jpg',
        }, {
            id: '4001009',
            name: '竹制干泡茶盘正方形沥水茶台品茶盘',
            price: '109.00',
            picture: 'https://yanxuan-item.nosdn.127.net/2d942d6bc94f1e230763e1a5a3b379e1.png',
        }, {
            id: '4001874',
            name: '古法温酒汝瓷酒具套装白酒杯莲花温酒器',
            price: '488.00',
            picture: 'https://yanxuan-item.nosdn.127.net/44e51622800e4fceb6bee8e616da85fd.png',
        }, {
            id: '4001649',
            name: '大师监制龙泉青瓷茶叶罐',
            price: '139.00',
            picture: 'https://yanxuan-item.nosdn.127.net/4356c9fc150753775fe56b465314f1eb.png',
        }, {
            id: '3997185',
            name: '与众不同的口感汝瓷白酒杯套组1壶4杯',
            price: '108.00',
            picture: 'https://yanxuan-item.nosdn.127.net/8e21c794dfd3a4e8573273ddae50bce2.jpg',
        }, {
            id: '3997403',
            name: '手工吹制更厚实白酒杯壶套装6壶6杯',
            price: '99.00',
            picture: 'https://yanxuan-item.nosdn.127.net/af2371a65f60bce152a61fc22745ff3f.jpg',
        }, {
            id: '3998274',
            name: '德国百年工艺高端水晶玻璃红酒杯2支装',
            price: '139.00',
            picture: 'https://yanxuan-item.nosdn.127.net/8896b897b3ec6639bbd1134d66b9715c.jpg',
        }, ]

        //1.数据渲染
        //封装函数  
        function render(arr) {
            //创建字符串变量
            let str = ''
                //遍历数据 利用forEach循环遍历数组里面的数据
            arr.forEach(function(item) {
                //console.log(item) 可以得到每个数组元素 对象
                //传递数据是使用数据解构
                const {name,price,picture} = item
                //拿到数据，利用字符串拼接生成结构添加到页面中
                str += `
                <div class="item">
                  <img src=${picture} alt="">
                  <p class="name">${name}</p>
                  <p class="price">${price}</p>
                </div>
                      `
            })
            document.querySelector('.list').innerHTML = str
        }
        render(goodsList)

        //2.过滤筛选
        document.querySelector('.filter').addEventListener('click', function(e) {
            //e.target.dataset.index // 1 2 3 根据点击data-index来判断
            //console.log(e.target)
            const {tagName,dataset} = e.target
                //console.log(tagName) // A
            if (tagName === 'A') {
                //arr 是返回的新数组
                let arr = goodsList
                    //console.log(dataset.index)
                if (dataset.index === '1') {
                    arr = goodsList.filter(item => item.price > 0 && item.price <= 100)
                } else if (dataset.index === '2') {
                    arr = goodsList.filter(item => item.price >= 100 && item.price <= 300)
                } else if (dataset.index === '3') {
                    arr = goodsList.filter(item => item.price >= 300)
                }

                //渲染函数
                render(arr)
            }
        })
    </script>
</body>
```





## JavaScript 进阶 

> 了解面向对象编程的基础概念及构造函数的作用，体会 JavaScript 一切皆对象的语言特征，掌握常见的对象属性和方法的使用。

- 了解面向对象编程中的一般概念
- 能够基于构造函数创建对象
- 理解 JavaScript 中一切皆对象的语言特征
- 理解引用对象类型值存储的的特征
- 掌握包装类型对象常见方法的使用

### 一、深入对象

> 了解面向对象的基础概念，能够利用构造函数创建对象。

#### 1.0 创建对象的三种方式

> 目标：了解创建对象有三种方式。

1. 利用对象字面量创建对象

```js
const obj = {
    name:'清',
    age:18
}
```

​    2.利用 new Object 创建对象

```js
const obj = new Object( {uname:'清',age:18} )
console.log(obj)  //{uname:'清',age:18}
```

​     3.利用构造函数创建对象



#### 1.1 构造函数

> 目标：能够利用构造函数创建对象。

> 构造函数是一种特殊的函数，主要用来初始化对象

- 构造函数是专门用于创建对象的函数，如果一个函数使用 `new` 关键字调用，那么这个函数就是构造函数。
- 使用场景：可以通过构造函数来 快速创建多个类似的对象

```html
<script>
  // 定义函数
  function foo() {
    console.log('通过 new 也能调用函数...');
  }
  // 调用函数
  new foo;
</script>
```

```js
//定义一个商品的构造函数
function Goods(name, price, count) {
      this.name = name
      this.price = price
      this.count = count
      this.sayhi = function () { }
    }
    //创建小米对象
    const mi = new Goods('小米', 1999, 20) //new 关键字调用函数
    console.log(mi)
    const hw = new Goods('华为', 3999, 59)
    console.log(hw)

//构造函数在技术上是常规函数
//不过有两个约定：
//1.他们的命名以大写字母开头
//2.他们只能由"new"操作符来执行
```

总结：

2. 使用 `new` 关键字调用函数的行为被称为实例化
3. 实例化构造函数时没有参数时可以省略 `()`
4. 构造函数内部无需写return, 返回值即为新创建的对象
5. 构造函数内部的 `return` 返回的值无效！所以不要写return
6. new Object()    new Date()  也是实例化构造函数

注：实践中为了从视觉上区分构造函数和普通函数，习惯将构造函数的首字母大写。

实例化执行过程：

1. 创建新对象
2. 构造函数 this 指向新对象
3. 执行构造函数代码，修改 this ，添加新的属性
4. 返回新对象

#### 1.2 实例成员

> 目标：能够说出什么是实例成员。

通过构造函数创建的对象称为实例对象，实例对象中的属性和方法称为实例成员。

```html
<script>
  // 构造函数
  function Person() {
    // 构造函数内部的 this 就是实例对象
    // 实例对象中动态添加属性
    this.name = '小明'
    // 实例对象动态添加方法
    this.sayHi = function () {
      console.log('大家好~')
    }
  }
  // 实例化，p1 是实例对象
  // p1 实际就是 构造函数内部的 this
  const p1 = new Person()
  console.log(p1)
  console.log(p1.name) // 访问实例属性
  p1.sayHi() // 调用实例方法
</script>
```

总结：

1. 构造函数内部 `this` 实际上就是实例对象，为其动态添加的属性和方法即为实例成员
2. 为构造函数传入参数，动态创建结构相同但值不同的对象
3. 实例对象中的属性和方法称为实例成员。

注：构造函数创建的实例对象彼此独立互不影响。

#### 1.3 静态成员

> 构造函数的属性和方法被称为静态成员。

在 JavaScript 中底层函数本质上也是对象类型，因此允许直接为函数动态添加属性或方法，构造函数的属性和方法被称为静态成员。

```html
<script>
  // 构造函数
  function Person(name, age) {
    // 省略实例成员
  }
  // 静态属性
  Person.eyes = 2
  Person.arms = 2
  // 静态方法
  Person.walk = function () {
    console.log('^_^人都会走路...')
    // this 指向 Person
    console.log(this.eyes)
  }
</script>
```

总结：

1. 静态成员指的是添加到构造函数本身的属性和方法
2. 一般公共特征的属性或方法静态成员设置为静态成员
3. 静态成员方法中的 `this` 指向构造函数本身



### 二、内置构造函数

> 掌握各引用类型和包装类型对象属性和方法的使用。

在 JavaScript 中**最主要**的数据类型有 6 种，分别是字符串、数值、布尔、undefined、null 和 对象，常见的对象类型数据包括数组和普通对象。其中字符串、数值、布尔、undefined、null 也被称为简单类型或基础类型，对象也被称为引用类型。

在 JavaScript 内置了一些构造函数，绝大部的数据处理都是基于这些构造函数实现的，JavaScript 基础阶段学习的 `Date` 就是内置的构造函数。

```html
<script>
  // 实例化
	let date = new Date();
  
  // date 即为实例对象
  console.log(date);
</script>
```

甚至字符串、数值、布尔、数组、普通对象也都有专门的构造函数，用于创建对应类型的数据。

#### 2.1 引用类型

> 引用类型： Object     Array    Date   等

##### Object

`Object` 是内置的构造函数，用于创建普通对象。

推荐使用字面量方式声明对象，而不是 `Object` 构造函数

```html
<script>
  // 通过构造函数创建普通对象
  const user = new Object({name: '小明', age: 15})

  // 这种方式声明的变量称为[字面量]  推荐使用！
  let student = {name: '清', age: 21}
  
  // 对象语法简写
  let name = '小红';
  let people = {
    // 相当于 name: name
    name,
    // 相当于 walk: function () {}
    walk () {
      console.log('人都要走路...');
    }
  }

  console.log(student.constructor);
  console.log(user.constructor);
  console.log(student instanceof Object);
    
    //想要获得对象里面的属性和值怎么做？
    for(let k in obj){
        console.log(k) //属性
        console.log(obj[k]) //属性值
    } 
    //现在有新的方法
</script>
```



三个静态方法：（静态方法就是只有构造函数Object可以调用的）

###### assign

> `Object.assign` 静态方法创建新的对象，常用于对象拷贝

```js
    //对象的拷贝
     const o = { uname: 'pink', age: 18 }
     const obj = {}
     Object.assign(obj, o) //把o 拷贝给 obj
     console.log(obj)  //{ uname: 'pink', age: 18 }

//使用：经常使用的场景给对象添加属性
      //给 o 新增属性
      const o = { uname: 'pink', age: 18 }
      Object.assign(o, { gender: '女' })
      console.log(o) //{ uname: 'pink', age: 18, gender: '女'}
```

###### keys

> `Object.keys` 静态方法获取对象中所有属性 (键)

```js
 const o = { uname: 'pink', age: 18 }
    // 获得所有的属性名(键) 并且返回的是一个数组
    console.log(Object.keys(o))  //返回数组['uname', 'age']
//注：返回的是一个数组！！！

```

###### values

> `Object.values` 表态方法获取对象中所有属性值

```js
 const o = { uname: 'pink', age: 18 }
 // 获得所有的属性值， 并且返回的是一个数组
 console.log(Object.values(o))  //  ['pink', 18]
//注：返回的是一个数组！！！
```



##### Array

`Array` 是内置的构造函数，用于创建数组。

```html
<script>
  // 构造函数创建数组
  let arr = new Array(5, 7, 8);
   console.log(arr)  //[5,7,8]
    
  // 字面量方式创建数组  建议使用字面量创建数组
  let list = ['html', 'css', 'javascript']

</script>
```

数组赋值后，无论修改哪个变量另一个对象的数据值也会相当发生改变。



###### reduce

- 作用：reduce  返回函数累计处理的结果，经常用于求和等

- 基本语法：

  ```js
  arr.reduce(function() { } , 起始值)
  ```

- 参数：起始值可以省略，如果写就作为第一次累计的起始值

- 语法：

  ```js
  arr.reduce(function(累计值，当前元素[,索引号][,原数组]) { } , 起始值)
  ```

- 累计值参数：

  1. 如果有起始值，则以起始值为准开始累计， 累计值  = 起始值

  2. 如果没有起始值， 则累计值以数组的第一个数组元素作为起始值开始累计

  3. 后面每次遍历就会用后面的数组元素 累计到 累计值 里面 （类似求和里面的 sum ）

     ```js
     //使用场景：求和运算
     const arr = [1, 2, 3]
         const re = arr.reduce((prev, item) => prev + item)
         console.log(re)  //6
     
     ```





总结：

1. 推荐使用字面量方式声明数组，而不是 `Array` 构造函数

2. 实例方法 `forEach` 用于遍历数组，替代 `for` 循环 (重点) 不返回，用于不改变值，经常用于查找打印输出值

3. 实例方法 `filter` 过滤数组单元值，生成新数组(重点)    筛选数组元素，并生成新数组

4. 实例方法 `map` 迭代原数组，生成新数组(重点)    返回新数组，新数组里面的元素是处理之后的值，经常用于处理数据

5. 实例方法 `join` 数组元素拼接为字符串，返回字符串(重点)

6. 实例方法  `find`  查找元素， 返回符合测试条件的第一个数组元素值，如果没有符合条件的则返回 undefined(重点)

7. 实例方法`every` 检测数组所有元素是否都符合指定条件，如果**所有元素**都通过检测返回 true，否则返回 false(重点)

8. 实例方法`some` 检测数组中的元素是否满足指定条件   **如果数组中有**元素满足条件返回 true，否则返回 false

9. 实例方法 `concat`  合并两个数组，返回生成新数组

10. 实例方法 `sort` 对原数组单元值排序

11. 实例方法 `splice` 删除或替换原数组单元

12. 实例方法 `reverse` 反转数组

13. 实例方法 `findIndex`  查找元素的索引值

    ```js
    //find
     const arr = ['red', 'blue', 'green']
         const re = arr.find(function (item) {
           return item === 'blue'
         })
         console.log(re) //blue
    
               const arr = [{
                    name: '小米',
                    price: 1999
                }, {
                    name: '华为',
                    price: 3999
                }, ]
                // 找小米 这个对象，并且返回这个对象
            const mi = arr.find(function(item) {
                    // console.log(item)  
                    // console.log(item.name)  
                    return item.name === '小米'
                })
                // find 查找
                // const mi = arr.find(item => item.name === '小米')
            console.log(mi)  //{name: '小米', price: 1999 }
    
    //every 
    //每一个是否都符合条件，如果都符合返回 true ，否则返回false
                  const arr1 = [10, 20, 30]
                 const flag = arr1.every(item => item >= 20)
                 console.log(flag) //false
    //案例
       const spec = {
           size: '40cm*40cm', 
           color: '黑色'
       }
        //1. 所有的属性值回去过来  数组
        // console.log(Object.values(spec))
        // 2. 转换为字符串   数组join('/') 把数组根据分隔符转换为字符串
        // console.log(Object.values(spec).join('/'))
        document.querySelector('div').innerHTML = Object.values(spec).join('/')   //40cm*40cm/黑色
    ```

    数组常见方法：为数组转换为真数组

    ```js
    //静态方法
    Array.form()
    
    //  Array.from(lis) 把伪数组转换为真数组
        const lis = document.querySelectorAll('ul li')
        // console.log(lis)
        // lis.pop() 报错
        const liss = Array.from(lis)
        liss.pop()
        console.log(liss)
    ```

    

#### 2.2 包装类型

> 包装类型： String  Number   Boolean  等

在 JavaScript 中的字符串、数值、布尔具有对象的使用特征，如具有属性和方法，如下代码举例：

```html
<script>
  // 字符串类型
  const str = 'hello world!'
 	// 统计字符的长度（字符数量）
  console.log(str.length)
  
  // 数值类型
  const price = 12.345
  // 保留两位小数
  price.toFixed(2) // 12.34
</script>
```

之所以具有对象特征的原因是字符串、数值、布尔类型数据是 JavaScript 底层使用 Object 构造函数“包装”来的，被称为包装类型。

##### String

`String` 是内置的构造函数，用于创建字符串。

```html
<script>
  // 使用构造函数创建字符串
  let str = new String('hello world!');

  // 字面量创建字符串
  let str2 = '你好，世界！';

  // 检测是否属于同一个构造函数
  console.log(str.constructor === str2.constructor); // true
  console.log(str instanceof String); // false
</script>
```

总结：

1. 实例属性 `length` 用来获取字符串的度长  (重点)
2. 实例方法 `split('分隔符')` 用来将字符串拆分成数组  (重点)
3. 实例方法 `substring（需要截取的第一个字符的索引[,结束的索引号]）` 用于字符串截取  (重点)
4. 实例方法 `startsWith(检测字符串[, 检测位置索引号])` 检测是否以某字符开头  (重点)
5. 实例方法 `includes(搜索的字符串[, 检测位置索引号])` 判断一个字符串是否包含在另一个字符串中，根据情况返回 true 或 false     (重点)
6. 实例方法 `toUpperCase` 用于将字母转换成大写
7. 实例方法 `toLowerCase` 用于将就转换成小写
8. 实例方法 `indexOf`  检测是否包含某字符
9. 实例方法 `endsWith` 检测是否以某字符结尾
10. 实例方法 `replace` 用于替换字符串，支持正则匹配
11. 实例方法 `match` 用于查找字符串，支持正则匹配

注：String 也可以当做普通函数使用，这时它的作用是强制转换成字符串数据类型。

```js
//1. split 把字符串 转换为 数组     和 join() 相反
    const str = 'pink,red'
    const arr = str.split(',')
    console.log(arr) //['pink','red']
    const str1 = '2022-4-8'
    const arr1 = str1.split('-')
    console.log(arr1)  //[2022,4,8]

// 2. 字符串的截取   substring(开始的索引号[， 结束的索引号])
    // 2.1 如果省略 结束的索引号，默认取到最后
    // 2.2 结束的索引号不包含想要截取的部分!!!
    const str = '今天又要做核酸了'
    console.log(str.substring(5, 7)) //核酸
    const str1 = 'qingqing'
    console.log(str.substring(3,4)) //g  

// 3. startsWith 判断是不是以某个字符开头
    const str = 'pink老师上课中'
    console.log(str.startsWith('pink')) //true
    const str1 = '#to be'
    console.log(str.startsWith('#')) //true
    console.log(str.startsWith('t'))  //false
    console.log(str.startsWith('t',1)) //true

// 4. includes 判断某个字符是不是包含在一个字符串里面
    const str = '我是pink老师'
    console.log(str.includes('pink')) // true
```



##### Number

`Number` 是内置的构造函数，用于创建数值。

```html
<script>
  // 使用构造函数创建数值
  let x = new Number('10')
  let y = new Number(5)

  // 字面量创建数值
  let z = 20

//常用方法：
  //toFixed() 设置保留小位数的长度
      const price = 12.345
      console.log(price.toFixed(2)) //12.35  //会四舍五入
</script>
```

总结：

1. 推荐使用字面量方式声明数值，而不是 `Number` 构造函数
2. 实例方法 `toFixed` 用于设置保留小数位的长度



### 渲染赠品案例

![image-20230123214457746](C:/Users/ASUS/Desktop/wb/web前端知识笔记~/js高级/assets/image-20230123214457746.png)





```js
<body>
  <div class="list">
    <!-- <div class="item">
      <img src="https://yanxuan-item.nosdn.127.net/84a59ff9c58a77032564e61f716846d6.jpg" alt="">
      <p class="name">称心如意手摇咖啡磨豆机咖啡豆研磨机 <span class="tag">【赠品】10优惠券</span></p>
      <p class="spec">白色/10寸</p>
      <p class="price">289.90</p>
      <p class="count">x2</p>
      <p class="sub-total">579.80</p>
    </div> -->
  </div>
  <div class="total">
    <div>合计：<span class="amount">1000.00</span></div>
  </div>
  <script>
    const goodsList = [
      {
        id: '4001172',
        name: '称心如意手摇咖啡磨豆机咖啡豆研磨机',
        price: 289.9,
        picture: 'https://yanxuan-item.nosdn.127.net/84a59ff9c58a77032564e61f716846d6.jpg',
        count: 2,
        spec: { color: '白色' }
      },
      {
        id: '4001009',
        name: '竹制干泡茶盘正方形沥水茶台品茶盘',
        price: 109.8,
        picture: 'https://yanxuan-item.nosdn.127.net/2d942d6bc94f1e230763e1a5a3b379e1.png',
        count: 3,
        spec: { size: '40cm*40cm', color: '黑色' }
      },
      {
        id: '4001874',
        name: '古法温酒汝瓷酒具套装白酒杯莲花温酒器',
        price: 488,
        picture: 'https://yanxuan-item.nosdn.127.net/44e51622800e4fceb6bee8e616da85fd.png',
        count: 1,
        spec: { color: '青色', sum: '一大四小' }
      },
      {
        id: '4001649',
        name: '大师监制龙泉青瓷茶叶罐',
        price: 139,
        picture: 'https://yanxuan-item.nosdn.127.net/4356c9fc150753775fe56b465314f1eb.png',
        count: 1,
        spec: { size: '小号', color: '紫色' },
        gift: '50g茶叶,清洗球,宝马, 奔驰'
      }
    ]

    // 1. 根据数据渲染页面
    //利用map来遍历，有多少条数据渲染多少商品
    //先写死数据 map返回的是数组，需要join('')转换为字符串，然后赋值给list
    document.querySelector('.list').innerHTML = goodsList.map(item => {
      // console.log(item)  // 每一条对象
      // 对象解构  item.price item.count
      const { picture, name, count, price, spec, gift } = item
      // 规格文字模块处理
      const text = Object.values(spec).join('/')
      // 计算小计模块 单价 * 数量  保留两位小数 
      // 注意精度问题，因为保留两位小数，所以乘以 100  最后除以100
      const subTotal = ((price * 100 * count) / 100).toFixed(2)
      // 处理赠品模块 '50g茶叶,清洗球'
     const str = gift ? gift.split(',').map(item => `<span class="tag">【赠品】${item}</span> `).join('') : ''
      return `
        <div class="item">
          <img src=${picture} alt="">
          <p class="name">${name} ${str} </p>
          <p class="spec">${text} </p>
          <p class="price">${price.toFixed(2)}</p>
          <p class="count">x${count}</p>
          <p class="sub-total">${subTotal}</p>
        </div>
      `
    }).join('')

    // 3. 合计模块
    const total = goodsList.reduce((prev, item) => prev + (item.price * 100 * item.count) / 100, 0)
    // console.log(total)
    document.querySelector('.amount').innerHTML = total.toFixed(2)
  </script>
</body>
```





## 深入面向对象 

> 了解构造函数原型对象的语法特征，掌握 JavaScript 中面向对象编程的实现方式，基于面向对象编程思想实现 DOM 操作的封装。

- 了解面向对象编程的一般特征
- 掌握基于构造函数原型对象的逻辑封装
- 掌握基于原型对象实现的继承
- 理解什么原型链及其作用
- 能够处理程序异常提升程序执行的健壮性

### 一、面向对象

> 学习 JavaScript 中基于原型的面向对象编程序的语法实现，理解面向对象编程的特征。

面向对象编程是一种程序设计思想，它具有 3 个显著的特征：封装、继承、多态。

#### 1.1 封装

封装的本质是将具有关联的代码组合在一起，其优势是能够保证代码复用且易于维护，函数是最典型也是最基础的代码封装形式，面向对象思想中的封装仍以函数为基础，但提供了更高级的封装形式。

##### 命名空间

先来回顾一下以往代码封装的形式：

```html
<script>
  // 普通对象（命名空间）形式的封装
  let beats = {
    name: '狼',
    setName: function (name) {
      this.name = this.name;
    },
    getName() {
      console.log(this.name);
    }
  }

  beats.setName('熊');
  beats.getName();
</script>
```

以往以普通对象（命名空间）形式封装的代码只是单纯把一系列的变量或函数组合到一起，所有的数据变量都被用来共享（使用 this 访问）。

##### 构造函数

> 封装是面向对象思想中比较重要的一部分，`js` 面向对象可以通过构造函数实现的封装。

> 同样的将变量和函数组合到了一起并能通过 this 实现数据的共享，所不同的是借助构造函数创建出来的实例对象之间是彼此不影响

对比以下通过面向对象的构造函数实现的封装：

```html
<script>
  function Person() {
    this.name = '清';
    this.setName = function (name) {// 设置名字
      this.name = name;
    }
    // 读取名字
    this.getName = () => {
      console.log(this.name);
    }
  }

  // 实例对像，获得了构造函数中封装的所有逻辑
  let p1 = new Person();
  p1.setName('小明');
  console.log(p1.name);

  // 实例对象
  let p2 = new Person();
  console.log(p2.name);
    
    
    function Star(uname, age) {
       this.uname = uname
       this.age = age
       this.sing = function() {
        console.log('我会唱歌')
         }
     } 
    //实例对象，获得了构造函数中封装的所有逻辑。
   const ldh = new Star('刘德华', 18)
   const zxy = new Star('张学友', 19)
   console.log(ldh.sing === zxy.sing) //false  说明两函数不一样 

</script>
```

- 封装是面向对象思想中比较重要的一部分，`js`面向对象可以通过构造函数实现的封装。
- 前面我们学过的构造函数方法很好用，但是 **存在浪费内存的问题**

同样的将变量和函数组合到了一起并能通过 this 实现数据的共享，所不同的是借助构造函数创建出来的实例对象之间是彼此不影响的。

总结：

1. 构造函数体现了面向对象的封装特性
2. 构造函数实例创建的对象彼此独立、互不影响
3. 命名空间式的封装无法保证数据的独立性

注：可以举一些例子，如女娲造人等例子，加深对构造函数的理解。

##### 原型对象

> 目标：能够利用原型对象实现方法共享

- 构造函数通过原型分配的函数是所有对象所 共享的。
- JavaScript 规定，每一个构造函数都有一个 prototype 属性，指向另一个对象，所以我们也称为原型对象
- 这个对象可以挂载函数，对象实例化不会多次创建原型上函数，节约内存
- 我们可以把那些不变的方法，直接定义在 prototype 对象上，这样所有对象的实例就可以共享这些方法。
- 构造函数和原型对象中的this 都指向 实例化的对象



实际上每一个构造函数都有一个名为 `prototype` 的属性，译成中文是原型的意思，`prototype` 的是对象类据类型，称为构造函数的原型对象，每个原型对象都具有 `constructor` 属性代表了该原型对象对应的构造函数。

```html
<script>
  function Person() {
    //公共的属性
  }

  // 每个函数都有 prototype 属性
  console.log(Person.prototype)  //返回一个对象称为原型对象
</script>
```



了解了 JavaScript 中构造函数与原型对象的关系后，再来看原型对象具体的作用，如下代码所示：

```html
<script>
  function Person() {
    // 此处未定义任何方法                       //1.公共的属性写到构造函数里面
  }                                      

  // 为构造函数的原型对象添加方法
  Person.prototype.sayHi = function () {     //2.公共的方法写到原型对象身上
    console.log('Hi~');
  }
	
  // 实例化
  let p1 = new Person();
  p1.sayHi(); // 输出结果为 Hi~
</script>
```



构造函数 `Person` 中未定义任何方法，这时实例对象调用了原型对象中的方法 `sayHi`，接下来改动一下代码：

```html
<script>
  function Person() {
    // 此处定义同名方法 sayHi
    this.sayHi = function () {
      console.log('嗨!');
    }
  }

    //构造函数 `Person` 中定义与原型对象中相同名称的方法，这时实例对象调用则是构造函中的方法 `sayHi`！！！
  // 为构造函数的原型对象添加方法
  Person.prototype.sayHi = function () { 
    console.log('Hi~');
  }   

  let p1 = new Person();
  p1.sayHi(); // 输出结果为 嗨!
</script>
```

构造函数 `Person` 中定义与原型对象中相同名称的方法，这时实例对象调用则是构造函中的方法 `sayHi`。

通过以上两个简单示例不难发现 JavaScript 中对象的工作机制：**当访问对象的属性或方法时，先在当前实例对象是查找，然后再去原型对象查找，并且原型对象被所有实例共享。**

```html
<script>
	function Person() {
    // 此处定义同名方法 sayHi
    this.sayHi = function () {
      console.log('嗨!' + this.name);
    }
  }

  // 为构造函数的原型对象添加方法
  Person.prototype.sayHi = function () {
    console.log('Hi~' + this.name);
  }
  // 在构造函数的原型对象上添加属性
  Person.prototype.name = '小明';

  let p1 = new Person();
  p1.sayHi(); // 输出结果为 嗨!
  
  let p2 = new Person();
  p2.sayHi();
</script>
```

总结：

1. **结合构造函数原型的特征，实际开发重往往会将封装的功能函数添加到原型对象中。**
2. 公共的属性写到构造函数里面
3. 公共的方法写到原型对象身上

###### 原型-this指向

> 构造函数 和 原型对象 中的this 都指向实例化的对象。

```js
let that
    function Star(uname) {
      this.uname = uname  //this指向实例化的对象 o
      that = this     
    }
    const o = new Star()
    console.log(that === o)

    // 原型对象里面的函数this指向的还是 实例对象 ldh
    let that
    function Star(uname) {
      this.uname = uname  
      that = this     
    }
    Star.prototype.sing = function () {
      that = this
    }
    // 实例对象 ldh   
    // 构造函数里面的 this 就是  实例对象  ldh
    const ldh = new Star('刘德华')
    ldh.sing()
    console.log(that === ldh)  //true
```

```js
    // 自己定义 数组扩展方法  求和 和 最大值 
    // 1. 我们定义的这个方法，任何一个数组实例对象都可以使用
    // 2. 自定义的方法写到  数组.prototype 身上

    // 1. 最大值
    const arr = [1, 2, 3]
    Array.prototype.max = function () {
      return Math.max(...this)  // 展开运算符
      // 原型函数里面的this 指向谁？ 实例对象 arr
    }

    // 2. 最小值
    Array.prototype.min = function () { 
      return Math.min(...this) 
    }
    console.log(arr.max())  //3
    console.log([2, 5, 9].max()) //9
    console.log(arr.min())  //1

    // 3. 求和 方法 
    Array.prototype.sum = function () {
      return this.reduce((prev, item) => prev + item, 0)
    }
    console.log([1, 2, 3].sum()) //6
    console.log([11, 21, 31].sum()) //63
```

###### constructor

> 该属性指向 该原型对象的构造函数。

每个原型对象里面都有个constructor 属性（constructor 构造函数）

```js
function Star(name) {
    this.name = name       ---------> //共享的属性和方法
    this.age = age         <---------  // constructor 属性
}
   //构造函数                             原型prototype
```

使用场景：如果有多个对象的方法，我们可以给原型对象采取对象形式赋值.但是这样就会覆盖构造函数原型对象原来的内容，这样修改后的原型对象 constructor  就不再指向当前构造函数了。此时，我们可以在修改后的原型对象中，添加一个 constructor 指向原来的构造函数。

```js
function Star(name) {
    this.name = name      
    this.age = age        
}
Star.prototype = {      
    sing:function() { console.log() },
    dance:function() {}
}
console.log(Star.prototype.constructor) //指向Object

function Star(name) {
    this.name = name      
    this.age = age        
}
Star.prototype = {      
    //手动利用constructor 指向 Star 构造函数
    constructor: Star                    
    sing:function() { console.log() },
    dance:function() {}
}
console.log(Star.prototype.constructor) //指向 Star
```

###### 对象原型

> 对象都会有一个属性`__ proto __ `指向构造函数的 prototype 原型对象。

> 之所以我们对象可以使用构造函数 prototype 原型对象的属性和方法，就是因为对象有 `__ proto __ `原型的存在。

注意：

1. `__ proto __ ` 是`js `非标准属性
2. [[prototype]]和`__ proto __ `意义相同
3. 用来表明当前实例对象指向哪个原型对象prototype
4. `__ proto __ `对象原型里面也有一个 constructor属性，**指向创建该实例对象的构造函数**



#### 1.2 继承

继承是面向对象编程的另一个特征，通过继承进一步提升代码封装的程度，JavaScript 中大多是借助原型对象实现继承的特性。

龙生龙、凤生凤、老鼠的儿子会打洞描述的正是继承的含义，分别封装中国人和日本人的行为特征来理解编程中继承的含义，代码如下：

```html
<script>
  // 封装中国人的行为特征
  function Chinese() {
    // 中国人的特征
    this.arms = 2;
    this.legs = 2;
    this.eyes = 2;
    this.skin = 'yellow';
    this.language = '中文';
    // 中国人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }

  // 封装日本人的行为特征
  function Japanese() {
    // 日本人的特征
    this.arms = 2;
    this.legs = 2;
    this.eyes = 2;
    this.skin = 'yellow';
    this.language = '日文';
    // 日本人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }
</script>
```

其实我们都知道无论是中国人、日本人还是其它民族，人们的大部分特征是一致的，然而体现在代码中时人的相同的行为特征被重复编写了多次，代码显得十分冗余，我们可以将重复的代码抽离出来：

##### 原型继承

基于构造函数原型对象实现面向对象的继承特性。

```html
<script>
  // 所有人
  function Person() {
    // 人的特征
    this.arms = 2
    this.legs = 2
    this.eyes = 2 
    // 人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }
  
  // 中国人
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
  // 日本人
	function Japanese() {
    this.skin = 'yellow';
    this.language = '日文';
  }
</script>
```

上述代码可以理解成将 `Chinese` 和 `Japanese` 共有的属性和方法提取出来了，也就是说 `Chinese` 和 `Japanese` 需要【共享】一些属性和方法，而原型对象的属性和方法恰好是可以被用来共享的，因此我们看如下代码：

```html
<script>
     // 人们共有的行为特征
  let people = {
    // 人的特征
    arms: 2,
    legs: 2,
    eyes:2,
    // 人的行为
    walk: function () {},
    sleep: function () {},
    sing: function () {}
  }
    
    
  // 中国人
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
  // 日本人
	function Japanese() {
    this.skin = 'yellow';
    this.language = '日文';
  }
  
  // 为 prototype 重新赋值
  Chinese.prototype = people //把公共的属性和方法给原型，这样就可以共享了*****
  Chinese.prototype.constructor = Chinese //注意让原型里的 constructor 重新指回Chinese *****
    const qing = new Chinese()
    console.log(qing)
</script>
```

如下图所示：

创建对象 `people` 将公共的的属性和方法独立出来，然后赋值给构造函数的 `prototype` 这样无论有多少个民族都可以共享公共的属性和方法了：

```html
<script>
  // 人们共有的行为特征
  let people = {
    // 人的特征
    arms: 2,
    legs: 2,
    eyes:2,
    // 人的行为
    walk: function () {},
    sleep: function () {},
    sing: function () {}
  }
  
  // 中国人
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
  // 日本人
	function Japanese() {
    this.skin = 'yellow';
    this.language = '日文';
  }
  
  function Englist() {
    this.skin = 'white';
    this.language= '英文';
  }
  
  // 中国人
  Chinese.prototype = people //把公共的属性和方法给原型，这样就可以共享了*****
  Chinese.prototype.constructor = Chinese //注意让原型里的 constructor 重新指回Chinese *****
  
  let c1 = new Chinese();
 	
  // 日本人
  Japanese.prototype = people //把公共的属性和方法给原型，这样就可以共享了*****
  Janpanese.prototype.constructor = Japanese //注意让原型里的 constructor 重新指回Japanese *****
  // 英国人
  English.prototype = people //把公共的属性和方法给原型，这样就可以共享了*****
  English.prototype.constructor = English //注意让原型里的 constructor 重新指回English *****
  
  // ...
</script>
```

继承是一种可以“不劳而获”的手段！！！上述代码中 `Chinese`、`Japanese`、`English` 都轻松的获得了 `people` 的公共的方法和属性，我们说 `Chinese`、`Japanese`、`English` 继承了 `people`。

上述代码中是以命名空间的形式实现的继承，事实上 JavaScript 中继承更常见的是借助构造函数来实现：

```html
<script>
  // 所有人
  function Person() {  //构造函数
    // 人的特征
    this.arms = 2;
    this.legs = 2;
    this.eyes = 2;
    // 人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }

  // 封装中国人的行为特征
  function Chinese() {
    // 中国人的特征
    this.skin = 'yellow';
    this.language = '中文';
  }

  // 封装日本人的行为特征
  function Japanese() {
    // 日本人的特征
    this.skin = 'yellow';
    this.language = '日文';
  }

  // human 是构造函数 Person 的实例
  let human = new Person()    ********

  // 中国人
  Chinese.prototype = human 
  Chinese.prototype.constructor = Chinese
  // 日本人
  Japanese.prototype = human
  Japanese.prototype.constructor = Japanese
</script>
```

继承完善写法

```js
//封装Person  (父类)
function Person() {  //构造函数
    // 人的特征
    this.arms = 2
    this.legs = 2
    this.eyes = 2
    // 人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
}
    //console.log(new Person()) //输出一个对象   // const Person = {  }

   //男人
   function Man() { } //子构造函数（子类）
   
    //用 new Person()替换刚才的固定对象!!!!!!
   Man.prototype = new Person()
    //注意让原型里的 constructor 重新指回Man *****
   Man.prototype.constructor = Man
   const pink = new Man()
   Man.prototype.smoking = function() {}
   console.log(pink)

   //女人 构造函数
   function Woman() {
       this.baby = function() { }
   }
   Woman.prototype = new Person()
   Woman.prototype.constructor = Woman
   const red = new Person()
   console.log(red)

//思路：
//真正做这个案例，我们的思路应该是先考虑大的，后考虑小的
//人类共有的属性和方法有那些，然后做个构造函数，进行封装
//一般公共属性写到构造函数内部
//公共方法，挂载到构造函数原型身上。
//男人继承人类的属性和方法，之后创建自己独有的属性和方法
//女人同理
```



##### 原型链

> 基于原型对象的继承使得不同构造函数的原型对象关联在一起，并且这种关联的关系是一种链状的结构，我们将原型对象的链状结构关系称为原型链。

```html
<script>
  // Person 构造函数
  function Person() {
    this.arms = 2;
    this.walk = function () {}
  }
	
  // Person 原型对象
  Person.prototype.legs = 2;
  Person.prototype.eyes = 2;
  Person.prototype.sing = function () {}
  Person.prototype.sleep = function () {}
	
  // Chinese 构造函数
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
	
  // Chinese 原型对象
  Chinese.prototype = new Person();
  Chinese.prototype.constructor = Chinese;
	
  // 实例化
  let c1 = new Chinese();

  console.log(c1);
</script>
```

在 JavaScript 对象中包括了一个非标准备的属性 `__proto__` 它指向了构造函数的原型对象，通过它可以清楚的查看原型对象的链状结构。

![image-20230124131233828](assets\image-20230124131233828.png)



原型链查找规则:

> 所有的对象里面都有`__ proto __ ` 对象原型，指向原型对象。

> 所有的原型对象里面有constructor , 指向创造该原型对象的构造函数。

1. 当访问一个对象的属性（包括方法）时，首先查找这个对象自身有没有该属性。
2. 如果没有就查找它的原型（也就是 `__ proto __ ` 指向的 prototype 原型对象）
3. 如果还没有就查找原型对象的原型（Object的原型对象）
4. 依此类推一直找到 Object 为止（null）
5. `__ proto __ ` 对象原型的意义就在于为对象成员查找机制提供一个方向，或者说一条路线
6. 可以使用 `instanceof `  运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上

```js
  console.log(ldh instanceof Star) //true
```



### 综合案例



![image-20230124132746556](assets\image-20230124132746556.png)

```js
<body>
  <button id="delete">删除</button>
  <button id="login">登录</button>

  <!-- <div class="modal">
    <div class="header">温馨提示 <i>x</i></div>
    <div class="body">您没有删除权限操作</div>
  </div> -->


  <script>
    // 1.  模态框的构造函数
    function Modal(title = '', message = '') {
      // 公共的属性部分
      this.title = title
      this.message = message
      // 因为盒子是公共的
      // 1. 创建 一定不要忘了加 this 
      this.modalBox = document.createElement('div')
      // 2. 添加类名
      this.modalBox.className = 'modal'
      // 3. 填充内容 更换数据
      this.modalBox.innerHTML = `
        <div class="header">${this.title} <i>x</i></div>
        <div class="body">${this.message}</div>
      `
      // console.log(this.modalBox)
    }
    // 2. 打开方法 挂载 到 模态框的构造函数原型身上
    Modal.prototype.open = function () {
      if (!document.querySelector('.modal')) {
        // 把刚才创建的盒子 modalBox  渲染到 页面中  父元素.appendChild(子元素)
        document.body.appendChild(this.modalBox)
        // 获取 x  调用关闭方法
        this.modalBox.querySelector('i').addEventListener('click', () => {
          // 箭头函数没有this 上一级作用域的this
          // 这个this 指向 m 
          this.close()
        })
      }
    }
    // 3. 关闭方法 挂载 到 模态框的构造函数原型身上
    Modal.prototype.close = function () {
      document.body.removeChild(this.modalBox)
    }

    // 4. 按钮点击
    document.querySelector('#delete').addEventListener('click', () => {
      const m = new Modal('温馨提示', '您没有权限删除')
      // 调用 打开方法
      m.open()
    })

    // 5. 按钮点击
    document.querySelector('#login').addEventListener('click', () => {
      const m = new Modal('友情提示', '您还么有注册账号')
      // 调用 打开方法
      m.open()
    })

  </script>
</body>
```





## JavaScript 进阶 

### 一、深浅拷贝

> 首先浅拷贝和深拷贝只针对引用类型

1. 首先浅拷贝和深拷贝只针对想Object,Array这样的复杂对象，简单来说，浅拷贝只复制一层对象的属性，二深拷贝则复制了所有的层级。
2. 对于字符串类型，浅复制是对值的复制，对于对象来说，浅复制是对对象地址的复制，并没   有开辟新的栈，也就是复制的结果是两个对象指向同一个地址，修改其中一个对象的属性，则另一个对象的属性也会 改变，而深复制则是开辟新的栈，两个对象对应两个不同的地址，修改一个对象的属性，不会改变另一个对象的属性。

~~~JavaScript
const obj = {
      uname: 'pink',
      age: 18,
      hobby: ['乒乓球', '足球'],
      family: {
        baby: '小pink'
      }
    }
    // 把对象转换为 JSON 字符串
    // console.log(JSON.stringify(obj))
    const o = JSON.parse(JSON.stringify(obj))
    console.log(o)
    o.family.baby = '123'
    console.log(obj)
~~~



##### 1.1 浅拷贝

> 浅拷贝：拷贝的是地址

> **如果是简单数据类型拷贝值，引用数据类型拷贝的是地址。**

常见方法：

1. 拷贝对象： `Object.assgin()` /     展开运算符  { ...obj }  拷贝对象
2. 拷贝数组：  `Array.prototype.concat() `  或者  [ ...arr ]

```js
// 展开运算符 { ...obj }
const obj = {
      uname: 'pink',
      age: 18,
      family: {
        baby: '小pink'
      }
    }
    // 浅拷贝
     const o = { ...obj }  
    // console.log(o)   
     o.age = 20
     console.log(o)  // {uname: 'pink',age: 20,family: {baby: '小pink'}}
     console.log(obj) //{uname: 'pink',age: 18,family: {baby: '小pink'}}


    const obj = {
      uname: 'pink',        //简单数据类型拷贝值
      age: 18,
      family: {
        baby: '小pink'      //引用数据类型拷贝地址
      }
    }
    const o = {}
    Object.assign(o, obj)
    o.age = 20
    o.family.baby = '老pink'
    console.log(o)  //{uname: 'pink',age: 20,family: {baby: '老pink'}}
    console.log(obj) //{uname: 'pink',age: 18,family: {baby: '老pink'}}  //老pink被修改了

   //更该对象里的方法还是会有影响
   //**如果是简单数据类型拷贝值，引用数据类型拷贝的是地址。**
   //(简单理解： 如果是单层对象，没问题，如果有多层就有问题)


```

总结：

- 直接赋值和浅拷贝有什么区别？
  1. 直接赋值的方法，只要是对象，都会相互影响，因为是直接拷贝对象栈里面的地址
  2. 浅拷贝如果是一层对象，不相互影响，如果出现多层对象拷贝还会相互影响

- 浅拷贝怎么理解？
  1. 拷贝对象之后，里面的属性值是简单数据类型直接拷贝值
  2. 如果属性值是引用数据类型则拷贝的是地址



##### 1.2 深拷贝

> 深拷贝：拷贝的是对象，不是地址

常见方法：

1. 通过递归实现深拷贝
2. `lodash`  /   `  cloneDeep` 
3. 通过  `  JSON.stringify()`   实现

```js
//1.通过递归实现深拷贝
const obj = {
      uname: 'pink',
      age: 18,
      hobby: ['乒乓球', '足球'],
      family: {
        baby: '小pink'
      }
    }
    const o = {}
    // 拷贝函数
    function deepCopy(newObj, oldObj) {
      debugger
      for (let k in oldObj) {
        // 处理数组的问题  一定先写数组 在写 对象 不能颠倒
        if (oldObj[k] instanceof Array) {
          newObj[k] = []
          //  newObj[k] 接收 []  hobby
          //  oldObj[k]   ['乒乓球', '足球']
          deepCopy(newObj[k], oldObj[k])
        } else if (oldObj[k] instanceof Object) {
          newObj[k] = {}
          deepCopy(newObj[k], oldObj[k])
        }
        else {
          //  k  属性名 uname age    oldObj[k]  属性值  18
          // newObj[k]  === o.uname  给新对象添加属性
          newObj[k] = oldObj[k]
        }
      }
    }
    deepCopy(o, obj) // 函数调用  两个参数 o 新对象  obj 旧对象
    console.log(o)
    o.age = 20
    o.hobby[0] = '篮球'
    o.family.baby = '老pink'
    console.log(obj)
    console.log([1, 23] instanceof Object)

//一定要先写Array后写Object
//当我们在进行简单类型拷贝的时候没问题，直接赋值就行了。
//但是如果遇到数组的拷贝，再次调用递归函数，如果遇到对象的形式，再次调用递归把对象解决。
```

```js
//2.`lodash`  /   `  cloneDeep` 
//js库 lodash 里面cloneDeep 内部实现了深拷贝
//1.先引入js文件

  <script src="./lodash.min.js"></script>
  <script>
    const obj = {
      uname: 'pink',
      age: 18,
      hobby: ['乒乓球', '足球'],
      family: {
        baby: '小pink'
      }
    }
      //语法： _.cloneDeep(要被克隆的对象)
    const o = _.cloneDeep(obj)
    console.log(o)
    o.family.baby = '老pink'
    console.log(obj)
  </script>
 
```

```js
//3.通过  `  JSON.stringify()`   实现
//JSON.parse(JSON.stringify(obj))

 const obj = {
      uname: 'pink',
      age: 18,
      hobby: ['乒乓球', '足球'],
      family: {
        baby: '小pink'
      }
    }
    // 把对象转换为 JSON 字符串
    // console.log(JSON.stringify(obj))
    const o = JSON.parse(JSON.stringify(obj))
    console.log(o)
    o.family.baby = '123'
    console.log(obj)
```




### 二、异常处理

> 了解 JavaScript 中程序异常处理的方法，提升代码运行的健壮性。

##### 2.1 throw

异常处理是指预估代码执行过程中可能发生的错误，然后最大程度的避免错误的发生导致整个程序无法继续运行。

```html
<script>
  function counter(x, y) {

    if(!x || !y) {
      // throw '参数不能为空!';
      throw new Error('参数不能为空!')
    }

    return x + y
  }

  counter()
</script>
```

总结：

1. `throw` 抛出异常信息，程序也会终止执行
2. `throw` 后面跟的是错误提示信息
3. `Error` 对象配合 `throw` 使用，能够设置更详细的错误信息

##### 2.2 try ... catch

捕捉错误信息（浏览器提供的错误信息）

```js
//try 试试 尝试 catch 拦住 finally 最后
<script>
   function foo() {
      try {
        // 查找 DOM 节点
        const p = document.querySelector('.p')
        p.style.color = 'red'
      } catch (error) {
        // try 代码段中执行有错误时，会执行 catch 代码段
        // 查看错误信息
        console.log(error.message)
        // 终止代码继续执行
        return

      }
      finally {           //fainlly不管是否有错，都会执行。
          alert('执行')
      }
      console.log('如果出现错误，我的语句不会执行')
    }
    foo()
</script>
```

总结：

1. `try...catch` 用于捕获错误信息
2. 将预估可能发生错误的代码写在 `try` 代码段中
3. 如果 `try` 代码段中出现错误后，会执行 `catch` 代码段，并截获到错误信息
4. `fainlly` 不管是否有错，都会执行。


### 三、this

> 了解函数中 this 在不同场景下的默认值，知道动态指定函数 this 值的方法。

#### 1.1 默认值

`this` 是 JavaScript 最具“魅惑”的知识点，不同的应用场合 `this` 的取值可能会有意想不到的结果，在此我们对以往学习过的关于【 `this` 默认的取值】情况进行归纳和总结。

##### 普通函数

**普通函数**的调用方式决定了 `this` 的值，即【谁调用 `this` 的值指向谁】，如下代码所示：

```js
<script>
  // 普通函数
  function sayHi() {
    console.log(this)  
  }
  // 函数表达式
  const sayHello = function () {
    console.log(this)
  }
  // 函数的调用方式决定了 this 的值
  sayHi() // window
  window.sayHi()
	

// 普通对象
  const user = {
    name: '小明',
    walk: function () {
      console.log(this)
    }
  }
  // 动态为 user 添加方法
  user.sayHi = sayHi
  uesr.sayHello = sayHello
  // 函数调用方式，决定了 this 的值
  user.sayHi()
  user.sayHello()
</script>

//严格模式
   'use strict'
    function fn() {
        console.log(this)  //undefined
    }
   fn()
//普通函数没有明确调用者时 `this` 值为 `window`，严格模式下没有调用者时 `this` 的值为 `undefined`。
```

注： 普通函数没有明确调用者时 `this` 值为 `window`，严格模式下没有调用者时 `this` 的值为 `undefined`。

##### 箭头函数

**箭头函数**中的 `this` 与普通函数完全不同，也不受调用方式的影响，**事实上箭头函数中并不存在 `this`** ！箭头函数中访问的 `this` 不过是箭头函数所在作用域的 `this` 变量。

1. 箭头函数会默认帮我们绑定外层 this 的值，所以在箭头函数中 this 的值和外层的 this 是一样的
2. 箭头函数中的this引用的就是最近作用域中的this
3. 向外层作用域中，一层一层查找this，直到有this的定义

```html
<script>
    
  console.log(this) // 此处为 window
  // 箭头函数
  const sayHi = function() {
    console.log(this) // 该箭头函数中的 this 为函数声明环境中 this 一致
  }
  // 普通对象
  const user = {
    name: '小明',
    // 该箭头函数中的 this 为函数声明环境中 this 一致
    walk: () => {
      console.log(this)
    },
    
    sleep: function () {
      let str = 'hello'
      console.log(this)
      let fn = () => {
        console.log(str)
        console.log(this) // 该箭头函数中的 this 与 sleep 中的 this 一致
      }
      // 调用箭头函数
      fn();
    }
  }

  // 动态添加方法
  user.sayHi = sayHi
  
  // 函数调用
  user.sayHi()
  user.sleep()
  user.walk()
</script>
```

在开发中【使用箭头函数前需要考虑函数中 `this` 的值】，**事件回调函数**使用箭头函数时，`this` 为全局的 `window`，因此DOM事件回调函数不推荐使用箭头函数，如下代码所示：

```html
<script>
  // DOM 节点
  const btn = document.querySelector('.btn')
  // 箭头函数 
  btn.addEventListener('click', () => {
    console.log(this) //此时 this 指向了 window
  })
  // 普通函数 
  btn.addEventListener('click', function () {
    console.log(this) //此时 this 指向了 DOM 对象
  }) 
</script>
```

同样由于箭头函数 `this` 的原因，**基于原型的面向对象也不推荐采用箭头函数**，如下代码所示：

```html
<script>
  function Person() {
  }
  // 原型对像上添加了箭头函数
  Person.prototype.walk = () => {
    console.log('人都要走路...')
    console.log(this); // window
  }
  const p1 = new Person()
  p1.walk()
</script>
```

总结：

1. 函数内不存在this，沿用上一级的，过程：向外层作用域中，一层一层查找this，直到有this的定义
2. 不适用==>构造函数，原型函数，字面量对象中函数，`dom`事件函数
3. 适用==>需要使用上层this的地方



#### this指向

以上归纳了普通函数和箭头函数中关于 `this` 默认值的情形，不仅如此 JavaScript 中还允许指定函数中 `this` 的指向，有 3 个方法可以动态指定普通函数中 `this` 的指向：

##### call

使用 `call` 方法调用函数，同时指定函数中 `this` 的值，使用方法如下代码所示：

语法：

```js
fun.call(thisArg, arg1, arg2, ...) 
 
//thisArg：在 fun 函数运行时指定的 this 值
//arg1，arg2：传递的其他参数
//返回值就是函数的返回值，因为它就是调用函数

```



```html
<script>
  // 普通函数
  function sayHi() {
    console.log(this);
  }

  let user = {
    name: '小明',
    age: 18
  }

  let student = {
    name: '小红',
    age: 16
  }

  // 调用函数并指定 this 的值
  sayHi.call(user); // this 值为 user
  sayHi.call(student); // this 值为 student

  // 求和函数
  function counter(x, y) {
    return x + y;
  }

  // 调用 counter 函数，并传入参数
  let result = counter.call(null, 5, 10);
  console.log(result);
</script>
```

总结：

1. `call` 方法能够在调用函数的同时指定 `this` 的值
2. 使用 `call` 方法调用函数时，第1个参数为 `this` 指定的值
3. `call` 方法的其余参数会依次自动传入函数做为函数的参数

##### apply

使用 `call` 方法**调用函数**，同时指定函数中 `this` 的值

语法：

```js
fun.apply(thisArg, [argsArray]) 
 
//thisArg：在 fun 函数运行时指定的 this 值
//argsArray：传递的值，必须包含在数组里面
//返回值就是函数的返回值，因为它就是调用函数

```

```html
<script>
  // 普通函数
  function sayHi() {
    console.log(this)
  }

  let user = {
    name: '小明',
    age: 18
  }

  let student = {
    name: '小红',
    age: 16
  }

  // 调用函数并指定 this 的值
  sayHi.apply(user) // this 值为 user
  sayHi.apply(student) // this 值为 student

  // 求和函数
  function counter(x, y) {
    return x + y
  }
  // 调用 counter 函数，并传入参数
  let result = counter.apply(null, [5, 10])
  console.log(result)
    
    //求数组最大值两个方法
    const arr = [3,5,2,9]
    console.log(Math.max.apply(null,arr))
    console.log(Math.max(...arr))
</script>
```

总结：

1. `apply` 方法能够在调用函数的同时指定 `this` 的值
2. 使用 `apply` 方法调用函数时，第1个参数为 `this` 指定的值
3. `apply` 方法第2个参数为数组，数组的单元值依次自动传入函数做为函数的参数

##### bind

`bind` 方法并**不会调用函数**，而是创建一个指定了 `this` 值的新函数

语法：

```js
fun.bind(thisArg, arg1, arg2, ...) 
         
//thisArg：在 fun 函数运行时指定的 this 值
// arg1，arg2：传递的其他参数
//返回由指定的 this 值和初始化参数改造的 原函数拷贝 （新函数）
//因此当我们只是想改变 this 指向，并且不想调用这个函数的时候，可以使用 bind，比如改变定时器内部的this指向.
         
 
    // 需求，有一个按钮，点击里面就禁用，2秒钟之后开启
    document.querySelector('button').addEventListener('click', function () {
      // 禁用按钮
      this.disabled = true
      window.setTimeout(function () {
        // 在这个普通函数里面，我们要this由原来的window 改为 btn
        this.disabled = false
      }.bind(this), 2000)   // 这里的this 和 btn 一样
    })
```

```html
<script>
  // 普通函数
  function sayHi() {
    console.log(this)
  }
  let user = {
    name: '小明',
    age: 18
  }
  // 调用 bind 指定 this 的值
  let sayHello = sayHi.bind(user);
  // 调用使用 bind 创建的新函数
  sayHello()   //返回值是个函数，但是这个函数的this是更改过的obj
</script>
```

注：`bind` 方法创建新的函数，与原函数的唯一的变化是改变了 `this` 的值。

总结：

相同点：都可以改变函数内部的this指向.

区别点：

1. call 和 apply  会调用函数, 并且改变函数内部this指向。
2. call 和 apply 传递的参数不一样, call 传递参数 aru1, aru2..形式  apply 必须数组形式[arg]
3. bind  不会调用函数, 可以改变函数内部this指向.

主要应用场景：

1. call  调用函数并且可以传递参数
2. apply 经常跟数组有关系.  比如借助于数学对象实现数组最大值最小值
3. bind  不调用函数,但是还想改变this指向. 比如改变定时器内部的this指向.


### 四、防抖节流

1. 防抖（debounce）
   所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间

  

2. 节流（throttle）
   所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数



Lodash 库 实现节流和防抖

```js
<div class="box"></div>
   //先引入js文件
  <script src="./lodash.min.js"></script>

  <script>
    const box = document.querySelector('.box')
    let i = 1  // 让这个变量++
    // 鼠标移动函数
    function mouseMove() {
      box.innerHTML = ++i
      // 如果里面存在大量操作 dom 的情况，可能会卡顿
    }

    // lodash 节流写法
     box.addEventListener('mousemove', _.throttle(mouseMove, 500))
    // lodash 防抖的写法
    box.addEventListener('mousemove', _.debounce(mouseMove, 500))

  </script>
```

##### 案例

页面打开，可以记录上一次的视频播放位置

两个事件:
①：ontimeupdate   事件在视频/音频（audio/video）当前的播放位置发送改变时触发 
②：onloadeddata  事件在当前帧的数据加载完成且还没有足够的数据播放视频/音频（audio/video）的下一帧时触发  

```js
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.21/lodash.min.js"></script>
  <script>
    // 1. 获取元素  要对视频进行操作
    const video = document.querySelector('video')
    video.ontimeupdate = _.throttle(() => {
      // console.log(video.currentTime) 获得当前的视频时间
      // 把当前的时间存储到本地存储
      localStorage.setItem('currentTime', video.currentTime)
    }, 1000)

    // 打开页面触发事件，就从本地存储里面取出记录的时间， 赋值给  video.currentTime
    video.onloadeddata = () => {
      // console.log(111)
      video.currentTime = localStorage.getItem('currentTime') || 0
    } 

  </script>
```



