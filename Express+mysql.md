# express

概念：使用 express 本地软件包，快速搭建 Web 服务 （基于 HTTP 模块）

功能：

1. 提供数据接口
2. 提供网页资源等

使用：

1. 下载 express 软件包 ` npm i express`

2. 导入 express 创建 Web 服务对象

3. 监听请求方法 和 请求路径

4. 对其他请求方法和请求路径，默认返回404提示

   ```js
   // 基于 express 创建 web 服务对象
   const express = require('express')
   const server = express()
   
   // 监听 get 请求路径为 / 时触发回调函数， 使用 send 响应内容
   server.get('/' , (req,res) => {
       res.send('你好，欢迎使用 Express')     // 中文不需要自己设置响应头了
   })
   
   // 监听所有请求方法和任意路径， 统一返回 404 提示
   server.all('*' , (req,res) => {
       res.status(404)
       res.send('你要访问的资源路径不存在')
   })
   
   // 监听端口号
   server.listen(3000 , () => {
       console.log('web 服务启动了')
   })
   ```

   

### 1. 初识express

概念：Express 是基于 `Node.js` 平台，快速、开放、极简的 Web 开发框架。

通俗的理解：Express 的作用和` Node.js `内置的 `http `模块类似，是**专门用来创建 Web 服务器的。**

**Express 的本质**：就是一个 `npm` 上的第三方`包`，提供了快速创建 Web 服务器的便捷方法。



**1.1 进一步理解 Express**

**不使用 Express 能否创建 Web 服务器?**   

 能，使用 Node.js 提供的原生 http 模块即可。

**有了 http 内置模块，为什么还有用 Express？** 

 http 内置模块用起来很复杂，开发效率低；Express 是基于内置的 http 模块进一步封装出来的，能够极大的提高开发效率。

**http 内置模块与 Express 是什么关系？** 

 后者是基于前者进一步封装出来的。



**1.2. Express 能做什么**

对于前端程序员来说，最常见的两种服务器，分别是：

⚫ Web 网站服务器：专门对外提供 Web 网页资源的服务器。

⚫ API 接口服务器：专门对外提供 API 接口的服务器。

使用 Express，我们可以方便、快速的创建 Web 网站的服务器或 API 接口的服务器。



### **2. Express 的基本使用**

##### 1.**安装**

在项目所处的目录中，运行如下的终端命令，即可将 express 安装到项目中使用：

```js
npm i express
```

##### 2.**创建基本的 Web 服务器**

```js
// 1. 导入 express
const express = require('express')

// 2. 创建 web 服务器
const app = express()

// 3.调用 app.listen(端口号，启动成功后的回调函数)，启动服务器
app.listen(3000, () => {
    console.log('express server sunning at http://127.0.0.1')
})
```

##### **3. 监听** **GET 请求**

通过 `app.get()` 方法，可以监听客户端的 GET 请求，具体的语法格式如下：

```js
// 参数1：客户端请求的 URL 地址
// 参数2：请求对应的处理函数
app.get('请求URL'， function(req,res) {
    // 处理函数
    // req: 请求对象（包含了与请求相关的属性与方法）
    // res：响应对象（包含了与响应相关的属性与方法）
})
```

##### **4. 监听** **POST 请求**

通过 `app.post() `方法，可以监听客户端的 POST 请求，具体的语法格式如下

```js
// 参数1：客户端请求的 URL 地址
// 参数2：请求对应的处理函数
app.post('请求URL'， function(req,res) {
    // 处理函数
    // req: 请求对象（包含了与请求相关的属性与方法）
    // res：响应对象（包含了与响应相关的属性与方法）
})
```

##### **5. 把内容响应给客户端**

通过 `res.send() `方法，可以把处理好的内容，发送给客户端：

```js
app.get('/user', (req,res) => {
    // 向客户端发送 JSON 对象
    res.send({ name: 'zs' , age: 20 , gender: '男'})
})

app.post('/user', (req,res) => {
    // 向客户端发送文本内容
    res.send('请求成功')
})
```

##### **6. 获取 URL 中携带的查询参数**

通过` req.query `对象，可以访问到客户端通过查询字符串的形式，发送到服务器的参数：

```js
app.get('/', (req,res) => {
    // req.query 默认是一个空对象
    // 客户端使用 ?name=zs&age=20 这种查询字符串形式，发送到服务器的参数
    // 可以通过 res.query 对象访问到,例如
    // req.query.name   req.query.age
    console.log(req.query)
    res.send(req.query)
})
```

##### **7. 获取 URL 中的动态参数**

通过 `req.params `对象，可以访问到 URL 中，通过 **:** 匹配到的动态参数

```js
// URL 地址中，可以通过 ：参数名 的形式， 匹配动态参数值
// :id 是一个动态的参数  ：后面的id不是固定的  可以有多个动态参数！
app.get('/user/:id' , (req,res) => {
    // req.params 默认是一个空对象
    // 里面存放着通过 ：动态匹配到的参数值
    console.log(req.params)
    res.send(req.params)
})
```



### **3.托管静态资源**

#### **1. `express.static()`**

 `express.static()`，通过它，我们可以非常方便地创建一个静态资源服务器，

例如，通过如下代码就可以将 public 目录下的图片、CSS 文件、JavaScript 文件对外开放访问了：

```js
app.use(express.static('public'))
```

**注意：**Express 在指定的静态目录中查找文件，并对外提供资源的访问路径。

因此，**存放静态文件的目录名(public)不会出现在 URL 中**。` http://localhost:3000/index.js`



#### **2. 托管多个静态资源目录**

如果要托管多个静态资源目录，请多次调用 `express.static() `函数：

```js
app.use(express.static('public'))
app.use(express.static('files'))
```

访问静态资源文件时，`express.static() `函数会**根据目录的添加顺序查找所需的文件**。



#### **3. 挂载路径前缀**

如果希望在托管的**静态资源访问路径之前**，挂载路径前缀，则可以使用如下的方式：

```js
app.use('/public' , express.static('public'))
```

现在，你就可以通过带有 /public 前缀地址来访问 public 目录中的文件了: `http://localhost:3000/clock/index.js`



### **4. `nodemon`**

**1. 为什么要使用 `nodemon`**

在编写调试 Node.js 项目的时候，如果修改了项目的代码，则需要频繁的手动 close 掉，然后再重新启动，非常繁琐。

现在，我们可以使用 nodemon（https://www.npmjs.com/package/nodemon） 这个工具，它能**够监听项目文件**

**的变动**，当代码被修改后，`nodemon `会**自动帮我们重启项目**，极大方便了开发和调试。



**2. 安装 `nodemon`**

在终端中，运行如下命令，即可将` nodemon `安装为全局可用的工具

```js
npm install -g nodemon
```



**3. 使用 `nodemon`**

当基于 Node.js 编写了一个网站应用的时候，传统的方式，是运行 node app.js 命令，来启动项目。这样做的坏处是：

代码被修改之后，需要手动重启项目。

现在，我们可以将 node 命令替换为 nodemon 命令，使用 nodemon app.js 来启动项目。这样做的好处是：**代码**

**被修改之后，会被 `nodemon` 监听到，从而实现自动重启项目的效果。**

```js
node app.js
#将上面的终端命令，替换为下面的终端命令，即可实现自动重启项目的效果
nodemon app.js
```



### **5. Express 路由**

#### **1. 路由的概念**

广义上来讲，路由就是映射关系。



**1.1. Express 中的路由**

在 Express 中，路由指的是**客户端的请求**与**服务器处理函数**之间的映射关系。

Express 中的路由分 3 部分组成，分别是请求的类型、请求的 URL 地址、处理函数，格式如下：

```html
app.METHOD(PATH,HANDLER)
app.请求类型(url,处理函数)
```



**1.2 . Express 中的路由的例子**

```js
// 匹配 GET 请求，且请求 URL 为 /
app.get('/' , function(req,res) {
    res.send('Hello World!')
})

// 匹配 POST 请求，且请求 URL 为 /
app.post('/' , function(req,res) => {
    res.send('Got a POST request')
})
```



**1.3. 路由的匹配过程**

每当一个请求到达服务器之后，**需要先经过路由的匹配**，只有匹配成功之后，才会调用对应的处理函数。

在匹配时，会按照路由的顺序进行匹配，如果**请求类型**和**请求的 URL** 同时匹配成功，则 Express 会将这次请求，转

交给对应的 function 函数进行处理。

路由匹配的注意点：

① 按照定义的先后顺序进行匹配

② **请求类型**和**请求的URL**同时匹配成功，才会调用对应的处理函数



#### **2. 路由的使用**

##### **1. 最简单的用法**

(随着挂载的路由越多，不用)

在 Express 中使用路由最简单的方式，就是把路由挂载到 `app` 上，示例代码如下：

```js
const express = require('express')
// 创建 web 服务器， 命名为 app
const app = express()

// 挂载路由
app.get('/' , (req,res) => { res.send('Hello World') })
app.post('/' , (req,res) => { res.send('Post Request') })

// 启动 web 服务器
app.listen(3000 , () => {
    console.log('server runnig at http://127.0.0.1')
})
```



##### **2. 模块化路由**

为了方便对路由进行模块化的管理，Express **不建议**将路由直接挂载到 app 上，而是推荐将路由抽离为单独的模块。

将路由抽离为单独模块的步骤如下：

① 创建路由模块对应的` .js `文件

② 调用 **express.Router()** 函数创建路由对象

③ 向路由对象上挂载具体的路由

④ 使用 module.exports 向外共享路由对象

⑤ 使用 `app.use() `函数注册路由模块



##### **3. 创建路由模块**

创建 `user.js` 路由模块

```js
// 1. 导入 express
const express = require('express')

// 2. 创建路由对象
const router = express.Router()

// 3. 挂载获取用户列表的路由
router.get('/user/list' , function(req,res) => {
           res.send('Get user list') 
})

// 4. 挂载添加用户列表的路由
router.post('/user/add' , function(req,res) => {
           res.send('Add new user') 
})

// 5. 向外导出路由对象
module.exports = router
```



##### **4. 注册路由模块**

```js
const express = require('express')
const app = express()

// 1. 导入路由模块
const userRouter = require('./router/user.js')

// 2. 使用 app.use() 注册路由模块  app.use() 函数的作用，就是用来注册全局中间件!!!!
app.use(userRouter)


app.listen(3000, () => {
    console.log('3000端口的服务器已经启动localhost:3000')
})
```



##### **5. 为路由模块添加前缀**

类似于托管静态资源时，为静态资源统一挂载访问前缀一样，路由模块添加前缀的方式也非常简单：

```js
// 1. 导入路由模块
const userRouter  = require('./router/user.js')

// 2. 使用 app.use() 注册路由模块， 并统一添加的访问前缀 /api
app.use('/api' , userRouter)
```





### 6. 中间件

##### 1.中间件的概念

**1.1  什么是中间件**

中间件（`Middleware` ），特指业务流程的**中间处理环节**。



**1.2. Express 中间件的调用流程**

![](C:\Users\ASUS\Desktop\web\webNotes\images\中间件.png)



**1.3. Express 中间件的格式**

![](C:\Users\ASUS\Desktop\web\webNotes\images\中间件2.png)



**1.4. next 函数的作用**

![](C:\Users\ASUS\Desktop\web\webNotes\images\中间件3.png)



##### **2. Express 中间件的初体验**

###### **1.** **定义中间件函数**

可以通过如下的方式，定义一个最简单的中间件函数：

```js
// 常量 mw 所指向的，就是一个中间件函数
const mw = function(req, res, next) {
    console.log('这是一个最简单的中间件函数')
        // 注意：
        // 在当前中间件的业务处理完毕后，必须调用 next() 函数
        // 表示把流转关系转交给下一个中间件或路由
    next()
}
```



###### **2.** **全局生效的中间件**

客户端发起的**任何请求**，到达服务器之后，**都会触发的中间件**，叫做全局生效的中间件。

通过调用` app.use`(中间件函数)，即可定义一个**全局生效**的中间件，示例代码如下：

```js
const express = require('express')
const app = express()

// 常量 mw 所指向的，就是一个中间件函数
const mw = function(req, res, next) {
    console.log('这是一个最简单的中间件函数')
        // 注意：
        // 在当前中间件的业务处理完毕后，必须调用 next() 函数
        // 表示把流转关系转交给下一个中间件或路由
    next()
}

// 将 mw 注册为全局生效的中间件
app.use(mw)

app.get('/', (req, res) => {
    console.log('调用了 / 这个路由')
    console.log('Hoem Page')
})

app.get('/user', (req, res) => {
    console.log('User Page')
})

app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动http://localhost:3000')
})
```



###### **3. 定义全局中间件的简化形式**

```js
// 全局注册中间件

app.use(function(req, res, next) {
    console.log('这是一个最简单的中间件和拿书')
    
    next()
})
```



###### **4. 中间件的作用**

![](C:\Users\ASUS\Desktop\web\webNotes\images\中间件5.png)

```js
const express = require('express')
const app = express()

app.use(function(req, res, next) {
    // 获取到 请求到服务器的时间
    const time = Date.now()
        // 为 req 对象，挂载自定义属性，从而把时间共享给后面的所有路由
    req.startTime = time
    next()
})

app.get('/', (req, res) => {
    res.send('Home page' + req.startTime)
})

app.get('/user', (req, res) => {
    res.send('User page' + req.startTime)
})

app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动http://localhost:3000')
})
```



###### **5. 定义多个全局中间件**

可以使用`app.use() `**连续定义多个全局中间件**。客户端请求到达服务器之后，会按照中间件**定义的先后顺序依次进行**

**调用**，示例代码如下：

```js
const express = require('express')
const app = express()


app.use(function(req, res, next) { // 第一个全局中间件
    console.log('调用了第1个全局中间件')
    next()
})
app.use(function(req, res, next) { // 第二个全局中间件
    console.log('调用了第2个全局中间件')
    next()
})

// 定义一个路由
app.get('/user', (req, res) => { // 请求这个路由，会依次触发上述两个全局中间件
    res.send('User Page')
})

app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动')
})
```



###### **6.** **局部生效的中间件**

**不使用** `app.use() `定义的中间件，叫做局部生效的中间件

```js
const express = require('express')
const app = express()

// 定义中间件函数 mw1
const mw1 = function(req, res, next) {
    console.log('这是中间件函数')
    next()
}

// mw1 这个中间件只在 '当前路由中生效'，这种用法属于"局部生效的中间件"
app.get('/', mw1, function(req,res) => {
      res.send('Home page')        
})

// mw1 这个中间件不会影响下面这个路由
app.get('/user' , function(req,res) {
    res.send('User page')
})

app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动')
})
```



###### **7. 定义多个局部中间件**

可以在路由中，通过如下两种**等价**的方式，**使用多个局部中间件**：

```js
// 以下两种方法是"完全等价"的， 可个根据自己的喜好，选择任意一种方式进行使用

app.get('/' , mw1,mw2, (req,res) => { res.send('Home page') })

app.get('/' , [mw1,mw2], (req,res) => { res.send('Home page') })
```



###### **8. 了解中间件的5个使用注意事项**

① 一定要在**路由之前**注册中间件 , 写在后面就不会调用了

② 客户端发送过来的请求，**可以连续调用多个**中间件进行处理

③ 执行完中间件的业务代码之后，**不要忘记调用 next() 函数**

④ 为了**防止代码逻辑混乱**，调用 next() 函数后不要再写额外的代码

⑤ 连续调用多个中间件时，多个中间件之间，**共享** req 和 res 对象



##### **3. 中间件的分类**

为了方便大家**理解**和**记忆**中间件的使用，Express 官方把**常见的中间件用法**，分成了 5 大类，分别是：

① **应用级别**的中间件

② **路由级别**的中间件

③ **错误级别**的中间件

④ **Express 内置**的中间件

⑤ **第三方**的中间件



###### **1. 应用级别的中间件**

通过 `app.use() `或 `app.get() `或` app.post() `，绑定到 `app 实`例上的中间件，叫做应用级别的中间件，代码示例如下：

```js
// 应用级别的中间件（全局中间件）
app.use((req,res,next) => {
    next
})

// 应用级别的中间件（局部中间件）
app.get('/', mw1, (req,res) => {
    res.send('Home page')
})
```



###### **2. 路由级别的中间件**

绑定到**` express.Router()`** 实例上的中间件，叫做路由级别的中间件。它的用法和应用级别中间件没有任何区别。只不

过，**应用级别中间件是绑定到 `app` 实例上，路由级别中间件绑定到 router 实例上**，代码示例如下：

```js
const express = require('express')
const app = express()
const router = express.Router()

// 路由级别的中间件
router.use(function(req, res, next) => {
      console.log('Time:' , Date.now())
      next()
})

app.use('/', router)
```



###### **3. 错误级别的中间件**

**作用**：专门用来捕获整个项目中发生的异常错误，从而防止项目异常崩溃的问题。

**格式**：错误级别中间件的 function 处理函数中，必须有 4 个形参，形参顺序从前到后，分别是 **(err, req, res, next)**。

**注意：错误级别的中间件，必须注册在所有路由之后！**

```js
const express = require('express')
const app = express()

// 1. 路由
app.get('/' , function(req,res) {  
    // 1.1 抛出一个自定义的错误, 认为的制作错误
    throw new Error('服务器内部发生了错误！')
    
    // 以下代码不能正常执行
    res.send('Home page')
})

// 2. 定义错误级别的中间件，捕获整个项目的异常错误，从而防止程序的崩溃（全局中间件）
app.use(function(err, req, res, next) {
    // 2.1 在服务器打印错误消息
    console.log('发生了错误：' + err.message)
    
    // 2.2 向客户端响应错误相关的内容
    res.send('Error! ' + err.message)
})


app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动')
})
```



###### **4.`Express`内置的中间件***

自 Express 4.16.0 版本开始，Express 内置了 3 个常用的中间件，极大的提高了 Express 项目的开发效率和体验：

①**` express.static` **快速托管静态资源的内置中间件，例如： HTML 文件、图片、CSS 样式等

②**` express.json` **解析` JSON` 格式的请求体数据

③ **`express.urlencoded` **解析 URL-encoded 格式的请求体数据

```js
// 配置解析 application/json 格式数据的内置中间件
app.use(express.json())

// 配置解析 application/x-www-form-urlencoded 格式数据的内置中间件
app.use(express.urlencoded({ extended: false }))
```

`express.json()`

```js
const express = require('express')
const app = express()

// 注意： 处理错误的中间件，其他的中间件，必须在路由之前进行配置
// 通过 express.json() 这个中间件， 解析表单中的 JSON 格式的数据
app.use(express.json())

// 路由
app.post('/user', (req, res) => {
    // 在服务器， 可以使用 req.body 这个属性，来接收客户端发送过来的请求体数据
    // 默认情况下， 如果不配置解析表单数据的中间件，则 req.body 默认等于 undefined
    // console.log(req.body)  undefined

    // 配置了 express.json() 这个中间件 过后
    console.log(req.body)
    res.send('ok')
})

app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动')
})
```

![](C:\Users\ASUS\Desktop\web\webNotes\images\中间件6.png)



`express.urlencoded()`

```js
const express = require('express')
const app = express()

// 注意： 处理错误的中间件，其他的中间件，必须在路由之前进行配置
// 通过 express.urlencoded() 这个中间件， 来解析表单中的 url-encoded 格式中的数据
app.use(express.urlencoded({ extended: false }))


// 路由
app.post('/book', (req, res) => {
    // 在服务器端， 可以通过 req.body 来获取 JSON 格式的表单数据 url-encoded 格式的数据
    console.log(req.body)

    res.send('ok')
})

app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动')
})
```

![](C:\Users\ASUS\Desktop\web\webNotes\images\中间件8.png)





###### **5. 第三方的中间件**

非 Express 官方内置的，而是由第三方开发出来的中间件，叫做第三方中间件。在项目中，大家可以**按需下载**并**配置**

第三方中间件，从而提高项目的开发效率。

例如：在 express@4.16.0 之前的版本中，经常使用 body-parser 这个第三方中间件，来解析请求体数据。

使用步骤如下：

① 运行 `npm install body-parser `安装中间件

② 使用 require 导入中间件

③ 调用 `app.use() `注册并使用中间件



**注意：**Express 内置的` express.urlencoded `中间件，就是基于 body-parser 这个第三方中间件进一步封装出来的。

```js
const express = require('express')
const app = express()

// 1. 导入解析表单数据的中间件 body-parser
const parser = require('body-parser')

// 2. 使用 app.use() 注册中间件
app.use(parser.urlencoded({ extended: false }))

app.post('/user', (req, res) => {
    // 如果没有配置任何解析表单数据的中间件，则 req.body 默认等于 undefined
    console.log(req.body)

    res.send('ok')
})

app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动')
})
```





##### **4. 自定义中间件**

###### **1. 需求描述与实现步骤**

自己手动模拟一个类似于 `express.urlencoded `这样的中间件，来解析 POST 提交到服务器的表单数据。

实现步骤：

① 定义中间件

② 监听 req 的 data 事件

③ 监听 req 的 end 事件

④ 使用 querystring 模块解析请求体数据

⑤ 将解析出来的数据对象挂载为 req.body

⑥ 将自定义中间件封装为模块



###### **2. 定义中间件**

使用` app.use() `来定义全局生效的中间件，代码如下：

```js

app.use(function(req, res, next) {
    // 中间件的业务逻辑
})
```



###### **3. 监听 req 的data事件**

在中间件中，需要监听 req 对象的 **data** 事件，来获取客户端发送到服务器的数据。

如果数据量比较大，无法一次性发送完毕，则客户端会**把数据切割后**，**分批发送到服务器**。所以 data 事件可能会触

发多次，每一次触发 data 事件时，**获取到数据只是完整数据的一部分**，需要手动对接收到的数据进行拼接。

```js
// 1. 定义变量， 用来存储客户端发送过来的请求体数据
let str = ''

// 2. 监听 req 对象的 data 事件 （客户端发送过来的新的请求体数据）
req.on('data' , (chunk) => {
    // 拼接请求体数据，隐式转化为字符串
    str += chunk
})
```



###### **4. 监听 req 的 end 事件**

当请求体数据**接收完毕**之后，会自动触发 req 的 end 事件。

因此，我们可以在 req 的 end 事件中，**拿到并处理完整的请求体数据**。示例代码如下:

```js
// 1. 监听 req 对象的 end 事件 （请求体发送完毕后自动触发）
req.on('end' , () => {
    // 打印完整的请求体数据
    console.log(str)
    
    // TODO: 把字符串格式的请求体数据，解析成对象格式
})
```



###### **5. 使用 querystring 模块解析请求体数据**

Node.js 内置了一个 **`querystring `**模块，**专门用来处理查询字符串**。通过这个模块提供的 **parse()** 函数，可以轻松把

查询字符串，解析成对象的格式。示例代码如下：

```js
// 导入处理 querystring 的 Node.js 内置模块
const qs = require('querystring')

// 调用 qs.parse() 方法，把查询字符串解析为对象
const body = qs.parse(str)
```



###### **6. 将解析出来的数据对象挂载为req.body**

**上游**的中间件和**下游**的中间件及路由之间，**共享同一份 req 和 res**。因此，我们可以将解析出来的数据，挂载为 req 

的自定义属性，命名为 **req.body**，供下游使用。

```js
req.on('end' , () => {
    const body = qs.parse(str)  // 调用 qs.parse() 方法，把查询字符串解析为对象
    req.body = body             // 将解析出来的请求体对象， 挂载为 req.body 属性
    next()                      // 最后，一定要调用 next() 函数， 执行后续的业务逻辑
})
```



###### **7. 将自定义中间件封装为模块**

为了优化代码的结构，我们可以把自定义的中间件函数，**封装为独立的模块**。

```js
// custom-body-parse.js 模块中的代码

  // 导入处理 querystring 的 Node.js 内置模块
  const qs = require('querystring')


  // 这是解析表单数据的中间件
  const bodyParser = function(req, res, next) {
      // 实现中间件具体的业务逻辑
      // 1. 定义变量， 用来存储客户端发送过来的请求体数据
      let str = ''

      // 2. 监听 req 对象的 data 事件 （客户端发送过来的新的请求体数据）
      req.on('data', (chunk) => {
          // 拼接请求体数据，隐式转化为字符串
          str += chunk
      })

      // 3. 监听 req 对象的 end 事件 （请求体发送完毕后自动触发）
      req.on('end', () => {
          // 在 str 中存放的是完整的请求体数据
          // console.log(str)

          // TODO: 把字符串格式的请求体数据，解析成对象格式

          // 调用 qs.parse() 方法，把查询字符串解析为对象
          const body = qs.parse(str)
              //console.log(body)
          req.body = body // 将解析出来的请求体对象， 挂载为 req.body 属性
          next() // 最后，一定要调用 next() 函数， 执行后续的业务逻辑
      })
  }

  // 向外导出解析请求体数据的中间件函数
  module.exports = bodyParser

```

![](images\自定义中间件.png)







### **7. 使用 Express 写接口**



##### **1. 创建基本的服务器**

```js
// 导入 express 模块
const express = require('express')

// 创建 express 的服务器实例
const app = express()

// write your code here...

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(3000, function() {
    console.log('Express server running at http://127.0.0.1')
})
```



##### **2. 创建 API 路由模块**

```js
// userRouter.js 路由模块
const express = require('express')
// 创建 router 的实例对象
const userRouter = express.Router()

// bind your router here...
// 在这里挂载对应的路由


module.exports = userRouter
```

```js
// app.js  
// 导入路由模块
const userRouter = require('./router/userRouter.js')

// 把路由模块，注册到 app 上
app.use('/api' , userRouter)
```



##### **3. 编写 GET 接口**

```js
userRouter.get('/get' , (req,res) => {
    // 1. 获取到客户端通过查询字符串，发送到服务器的数据
    const query = req.query
    
    // 2. 调用 res.send() 方法，把数据响应给客户端
    res.send({
        status: 200,        // 业务状态码
        msg:'GET请求成功!',   // 状态描述
        data: query         // 需要响应给客户端的具体数据
    })
})
```



##### **4. 编写 POST 接口**

```js
userRouter.post('/post' , (req,res) => {
    // 1. 获取客户端通过请求体，发送到服务器的 URL-encoded 数据
    const body = req.body
    
    // 2. 调用 res.send() 方法，把数据响应给客户端
    res.send({
        status: 200,        // 业务状态码
        msg:'POST请求成功!',   // 状态描述消息
        data: body           // 需要响应给客户端的具体数据
    })
})
```

注意：如果要获取` URL-encoded `格式的请求体数据，必须配置中间件 **app.use(express.urlencoded({ extended: false }))**

**在全局导入路由模块之前配置中间件。**



###  **8. CORS 跨域资源共享**

##### **1. 接口的跨域问题**

刚才编写的 GET 和 POST接口，存在一个很严重的问题：**不支持跨域请求。**

解决接口跨域问题的方案主要有两种：

① CORS（主流的解决方案，推荐使用）

② JSONP（有缺陷的解决方案：只支持 GET 请求）



##### **2. 使用cors 中间件解决跨域问题**

cors 是 Express 的一个第三方中间件。通过安装和配置 cors 中间件，可以很方便地解决跨域问题。

使用步骤分为如下 3 步：

① 运行 **npm install cors**   安装中间件

② 使用 **const cors = require('cors')**   导入中间件

③ **在路由之前调用 app.use(cors())   配置中间件**

```js
const express = require('express')
const app = express()

// 配置解析表单数据的中间件
app.use(express.urlencoded({ extended: false }))

// 一定要在路由之前， 配置 cors 中间件，从而解决接口跨域的问题
const cors = require('cors')
app.use(cors())

// 导入路由模块
const router = require('./router/user')
// 把路由模块注册到 app 上
app.use('/api', router)

// 启动服务器
app.listen(3000, () => {
    console.log('3000端口的web服务器已经启动')
})
```



##### **3. 什么是 CORS**

![](images\cors1.png)



##### **4. CORS 的注意事项**

① CORS 主要在**服务器端**进行配置。客户端浏览器**无须做任何额外的配置**，即可请求开启了 CORS 的接口。

② CORS 在浏览器中有兼容性。只有支持 XMLHttpRequest Level2 的浏览器，才能正常访问开启了 CORS 的服务端接口。



##### **5. CORS 响应头部 ** 

######       **1. Access-Control-Allow-Origin** 

![](images\cors2.png)

![](images\cors3.png)

###### **2. Access-Control-Allow-Headers**

![](images\cors4.png)



###### **3. Access-Control-Allow-Methods**

![](images\cors5.png)





##### **6. CORS请求的分类**

客户端在请求 CORS 接口时，根据**请求方式**和**请求头**的不同，可以将 CORS 的请求分为两大类，分别是：

① 简单请求

② 预检请求



###### **1. 简单请求**

同时满足以下两大条件的请求，就属于简单请求：

① **请求方式**：GET、POST、HEAD 三者之一

② **HTTP 头部信息**不超过以下几种字段：无自定义头部字段、Accept、Accept-Language、Content-Language、DPR、

Downlink、Save-Data、Viewport-Width、Width 、**Content-Type**（只有三个值application/x-www-form-urlencoded、multipart/form-data、text/plain）



###### **2. 预检请求**

只要符合以下任何一个条件的请求，都需要进行预检请求：

① 请求方式为 GET、POST、HEAD 之外的请求 Method 类型

② 请求头中**包含自定义头部字段**

③ 向服务器发送了 **application/json 格式的数据**

在浏览器与服务器正式通信之前，浏览器会**先发送 OPTION 请求进行预检，以获知服务器是否允许该实际请求**，所以这一

次的 OPTION 请求称为“预检请求”。**服务器成功响应预检请求后，才会发送真正的请求，并且携带真实数据。**



**区别**

**简单请求的特点**：客户端与服务器之间只会发生一次请求。

**预检请求的特点**：客户端与服务器之间会发生两次请求，OPTION 预检请求成功之后，才会发起真正的请求。





##### 7. JSONP接口

**JSONP 的概念与特点**

**概念**：浏览器端通过 <script> 标签的 src 属性，请求服务器上的数据，同时，服务器返回一个函数的调用。

这种请求数据的方式叫做 JSONP。

**特点**：

① JSONP 不属于真正的 Ajax 请求，因为它没有使用 XMLHttpRequest 这个对象。

② JSONP 仅支持 GET 请求，不支持 POST、PUT、DELETE 等请求。





# MYSQL

### **1. 数据库的基本概念**

#### **1.1 什么是数据库**

数据库（database）是用来**组织**、**存储**和**管理**数据的仓库。

当今世界是一个充满着数据的互联网世界，充斥着大量的数据。数据的来源有很多，比如出行记录、消费记录、

浏览的网页、发送的消息等等。除了文本类型的数据，图像、音乐、声音都是数据。

为了方便管理互联网世界中的数据，就有了数据库管理系统的概念（简称：数据库）。用户可以对数据库中的数

据进行**新增、查询、更新、删除**等操作。



#### **1.2 常见的数据库及分类**

市面上的数据库有很多种，最常见的数据库有如下几个：

⚫ MySQL 数据库（目前使用最广泛、流行度最高的开源免费数据库；Community + Enterprise）

⚫ Oracle 数据库（收费）

⚫ SQL Server 数据库（收费）

⚫ Mongodb 数据库（Community + Enterprise）

其中，MySQL、Oracle、SQL Server 属于**传统型数据库**（又叫做：关系型数据库 或 SQL 数据库），这三者的

设计理念相同，用法比较类似。

而 **Mongodb** 属于**新型数据库**（又叫做：非关系型数据库 或 NoSQL 数据库），它在一定程度上弥补了传统型

数据库的缺陷。



#### **1.3 传统型数据库的数据组织结构**

数据的组织结构：指的就是数据以什么样的结构进行存储。

传统型数据库的数据组织结构，与 Excel 中数据的组织结构比较类似。

因此，我们可以对比着 Excel 来了解和学习传统型数据库的数据组织结构。



###### **1. Excel 的数据组织结构**

![](images\excel1.png)



###### **2. 传统型数据库的数据组织结构**

![](images\excel2.png)



###### **3. 实际开发中库、表、行、字段的关系**

① 在实际项目开发中，一般情况下，每个项目都对应独立的数据库。

② 不同的数据，要存储到数据库的不同表中，例如：用户数据存储到 users 表中，图书数据存储到 books 表中。

③ 每个表中具体存储哪些信息，由字段来决定，例如：我们可以为 users 表设计 id、username、password 这 3 个字段。

④ 表中的行，代表每一条具体的数据。



###### **4. 安装并配置 MySQL**

对于开发人员来说，只需要安装 MySQL Server 和 MySQL Workbench 这两个软件，就能满足开发的需要了。

⚫ **MySQL Server**：专门用来提供数据存储和服务的软件。

⚫ **MySQL Workbench**：可视化的 MySQL 管理工具，通过它，可以方便的操作存储在 MySQL Server 中的数据。





### **2. MySQL 的基本使用**



#### **2.1 使用 MySQL Workbench 管理数据库**

###### **1.** **连接数据库**

![](images\mysql1.png)



###### **2. 了解主界面的组成部分**

![](images\mysql2.png)



###### **3. 创建数据库**

![](images\mysql3.png)



![](images\mysql5.png)





###### **4. 创建数据表**

![](images\mysql6.png)



###### **5. 向表中写入数据**

![](images\mysql7.png)







####  **2.2 使用 SQL 管理数据库**

**1. 什么是 SQL**

SQL（英文全称：Structured Query Language）是结构化查询语言，专门用来访问和处理数据库的编程语言。能够让我们**以编程的形式**，**操作数据库里面的数据**。

① SQL 是一门**数据库编程语言**

② 使用 SQL 语言编写出来的代码，叫做 **SQL 语句**

③ SQL 语言**只能在关系型数据库中使用**（例如 MySQL、Oracle、SQL Server）。非关系型数据库（例如 Mongodb）                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       不支持 SQL 语言



**2. SQL 能做什么**

① 从数据库中**查询数据**

② 向数据库中**插入新的数据**

③ **更新**数据库中的**数据**

④ 从数据库**删除数据**

⑤ 可以创建新数据库

⑥ 可在数据库中创建新表

⑦ 可在数据库中创建存储过程、视图

⑧ etc…



**3. SQL 的学习目标**

重点掌握如何使用 SQL 从数据表中：

**查询数据（select） 、插入数据（insert into） 、更新数据（update） 、删除数据（delete）**

额外需要掌握的 4 种 SQL 语法：

**where 条件、and 和 or 运算符、order by 排序、count(*) 函数**





#### **2.3 SELECT **

![](images\select1.png)

![](images\select2.png)

![](images\select3.png)

#### **2.4 INSERT INTO**

![](images\insert1.png)

![](images\insert2.png)



#### **2.5 UPDATE**

![](images\update.png)

![](images\update2.png)

![](images\update3.png)





#### **2.6 DELETE**

![](images\delete.png)

![](images\delete1.png)



#### **2.7 WHERE**



![](images\where1.png)

![](images\where2.png)

![](images\where3.png)



#### **2.8 AND** **和** **OR**

![](images\and1.png)

![](images\and2.png)

![](images\and3.png)





#### **2.9 ORDER BY**

![](images\order1.png)

![](images\order2.png)

![](images\order3.png)

![](images\order4.png)





#### **2.10 COUNT(\*)** 

![](images\count1.png)

![](images\count2.png)



![](images\count3.png)





### **3. 在项目中操作 MySQL**



#### **3.1 在项目中操作数据库的步骤**

① 安装操作 MySQL 数据库的第三方模块（**mysql**）

② 通过 mysql 模块**连接到 MySQL 数据库**

③ 通过 mysql 模块**执行 SQL 语句**

![](images\lian1.png)



#### **3.2 安装与配置 mysql 模块**

###### **1.** **安装** **mysql 模块**

**mysql** 模块是托管于 npm 上的**第三方模块**。它提供了在 Node.js 项目中**连接**和**操作** **MySQL** 数据库的能力。

想要在项目中使用它，需要先运行如下命令，将 mysql 安装为项目的依赖包：

```js
npm install mysql
```



###### **2.** **配置** **mysql 模块**

在使用 mysql 模块操作 MySQL 数据库之前，**必须先对 mysql 模块进行必要的配置**，主要的配置步骤如下：

```js
// 1. 导入 mysql 模块
const mysql = require('mysql')

// 2. 建立与 mysql 数据的链接
const db = mysql.createPool({
    host: '127.0.0.1',      // 数据库的 IP 地址
    user: 'root',           // 登录数据库的账号
    password: 'admin123',   // 登录数据库的密码
    database: 'my_db_01'    // 指定要操作那个数据库
})
```



###### **3. 测试 mysql 模块能否正常工作**

调用 **db.query()** 函数，指定要执行的 SQL 语句，通过回调函数拿到执行的结果：

```js
// 检测 mysql 模块能否正常工作
db.query('select 1' , (err , results) => {
    if(err) return console.log(err.message)
    
    // 只要能打印出 [ RowDataPacket { '1' : 1 } ] 的结果，就证明数据库连接正常
    console.log(results)
})
```



####  **3.3 使用 mysql 模块操作 MySQL 数据库**



###### **1. 查询数据**

查询 users 表中所有的数据：

```js
// 查询 users 表中的所有的用户数据

db.query('select * from users' , (err , results) => {
    // 查询失败
    if(err) return console.log(err.message)
    // 查询成功
    console.log(results)
})
```

**注意：如果执行的是 select 查询语句，则执行的结果是数组！**



###### **2. 插入数据**

向 users 表中新增数据， 其中 **username** 为 Spider-Man，**password** 为 pcc321。示例代码如下：

```js
// 1. 要插入到 users 表中的数据对象
const user = { username: 'Spider-Man', password: 'pcc321', status: 105 }

// 2. 待执行的 sql 语句， 其中英文的 ? 代表占位符
const sqlStr = 'insert into users (username , password, status) values (?,?,?)'

// 3. 使用数组的形式， 依次为 ? 占位符指定具体的值
db.query(sqlStr, [user.username, user.password, user.status], (err, results) => {
    if (err) return console.log(err.message) // 失败
        // 成功
    if (results.affectedRows === 1) {
        console.log('插入数据成功！')
    }
})
```

**注意：如果执行的是 insert into 插入语句，则 results 是一个对象**

**可以通过 results.affectedRows 属性，来判断是否插入数据成功**



###### **3. 插入数据的便捷方式**

向表中新增数据时，如果**数据对象的每个属性**和**数据表的字段****一一对应**，则可以通过如下方式快速插入数据：

```js
// 1. 要插入到 users 表中的数据对象
const user = { username: 'dapeng', password: 'aao321', status: 0 }

// 2. 待执行的 sql 语句， 其中英文的 ? 代表占位符
const sqlStr = ' insert into users set ? '


// 3. 直接将数据对象当作占位符的值
db.query(sqlStr, user, (err, results) => {
    if (err) return console.log(err.message) // 失败
        // 成功
    if (results.affectedRows === 1) {
        console.log('插入数据成功！')
    }
})
```



###### **4. 更新数据**

```js
// 1. 要更新的数据对象 (更新 id 为 105的数据)
const user = { id: 105, username: 'xiaohei', password: '123abc', status: 1 }

// 2. 要执行的 sql 语句
const sqlStr = 'update users set username = ? , password = ? , status = ? where id = ?'

// 3. 调用 db.query() 执行 sql 语句的同时，使用数组依次为占位符指定具体的值
db.query(sqlStr, [user.username, user.password, user.status, user.id], (err, results) => {
    if (err) return console.log(err.message) // 失败
        // 成功
    if (results.affectedRows === 1) {
        console.log('更新数据成功!')
    }
})
```

**注意： 执行了 update 语句之后，执行的结果，也是一个对象，可以通过 affectedRows 判断是否更新成功！**



###### **5. 更新数据的便捷方式**

更新表数据时，如果**数据对象的每个属性**和**数据表的字段**一一对应，则可以通过如下方式快速更新表数据：

```js
// 1. 要更新的数据对象 
const user = { id: 105, username: 'xiaobai', password: '123456', status: 1 }

// 2. 要执行的 sql 语句
const sqlStr = 'update users set ? where id = ?'

// 3. 调用 db.query() 执行 sql 语句的同时，使用数组依次为占位符指定具体的值
db.query(sqlStr, [user, user.id], (err, results) => {
    if (err) return console.log(err.message) // 失败
        // 成功
    if (results.affectedRows === 1) {
        console.log('更新数据成功!')
    }
})
```



###### **6. 删除数据**

在删除数据时，推荐根据 id 这样的**唯一标识**，来删除对应的数据。示例如下：

```js
// 1. 要执行的 sql 语句
const sqlStr = 'delete from users where id = ?'

// 2. 调用 db.query() 执行 sql 语句的同时，为占位符指定具体的值
// 注意： 如果 sql 语句中有多个占位符，则必须使用数组为，为每个占位符指定具体的值
//       如果 sql 语句中只有一个占位符，则可以省略数组
db.query(sqlStr, 106 , (err , results) => {
     if (err) return console.log(err.message) // 失败
        // 成功
    if (results.affectedRows === 1) {
        console.log('删除数据成功!')
    }
})
```



###### **7.** **标记删除**

使用 DELETE 语句，会把真正的把数据从表中删除掉。为了保险起见，**推荐使用**标记删除的形式，来**模拟删除的动作**。

所谓的标记删除，就是在表中设置类似于 **status** 这样的**状态字段**，来**标记**当前这条数据是否被删除。

当用户执行了删除的动作时，我们并没有执行 DELETE 语句把数据删除掉，而是执行了 UPDATE 语句，将这条数据对应

的 status 字段标记为删除即可。

```js
// 标记删除
// 使用 update 语句替代 delete 语句 ， 只更新数据的状态，并没有真正的删除
db.query('update users set status =? where id = ?' , [1 , 101] , (err, results) => {
    if (err) return console.log(err.message) // 失败
        // 成功
    if (results.affectedRows === 1) {
        console.log('标记删除数据成功!')
    }
})
```



### **4. 前后端的身份认证**



##### **1. Web 开发模式**

目前主流的 Web 开发模式有两种，分别是：

① 基于**服务端渲染**的传统 Web 开发模式

② 基于**前后端分离**的新型 Web 开发模式



前后端分离的概念：前后端分离的开发模式，**依赖于 Ajax 技术的广泛应用**。简而言之，前后端分离的 Web 开发模式，

就是**后端只负责提供 API 接口，前端使用 Ajax 调用接口**的开发模式。

优点：

① **开发体验好。**前端专注于 UI 页面的开发，后端专注于api 的开发，且前端有更多的选择性。

② **用户体验好。**Ajax 技术的广泛应用，极大的提高了用户的体验，可以轻松实现页面的局部刷新。

③ **减轻了服务器端的渲染压力。**因为页面最终是在每个用户的浏览器中生成的。

缺点：

① **不利于 SEO。**因为完整的 HTML 页面需要在客户端动态拼接完成，所以爬虫对无法爬取页面的有效信息。（解决方

案：利用 Vue、React 等前端框架的 **SSR** （server side render）技术能够很好的解决 SEO 问题！）





##### **2. 身份认证**



**1. 什么是身份认证**

**身份认证**（Authentication）又称“**身份验证**”、“**鉴权**”，是指**通过一定的手段，完成对用户身份的确认**。

⚫ 日常生活中的身份认证随处可见，例如：高铁的验票乘车，手机的密码或指纹解锁，支付宝或微信的支付密码等。

⚫ 在 Web 开发中，也涉及到用户身份的认证，例如：各大网站的**手机验证码登录**、**邮箱密码登录**、**二维码登录**等。



**2. 为什么需要身份认证**

身份认证的目的，是为了**确认当前所声称为某种身份的用户，确实是所声称的用户**。

在互联网项目开发中，如何对用户的身份进行认证，是一个值得深入探讨的问题。



**3. 不同开发模式下的身份认证**

对于服务端渲染和前后端分离这两种开发模式来说，分别有着不同的身份认证方案：

① 服务端渲染推荐使用 **Session 认证机制**

② 前后端分离推荐使用 **JWT 认证机制**



##### **3. Session 认证机制**



###### **1. HTTP 协议的无状态性**

HTTP 协议的无状态性，指的是客户端**的每次 HTTP 请求都是独立的**，连续多个请求之间没有直接的关系，**服务器不会**

**主动保留每次 HTTP 请求的状态**。



###### **2.** **如何突破 HTTP 无状态的限制**

![](images\session1.png)



###### **3. 什么是 Cookie**

Cookie 是**存储在用户浏览器中的一段不超过 4 KB 的字符串**。它由一个名称（Name）、一个值（Value）和其它几个用

于控制 Cookie 有效期、安全性、使用范围的可选属性组成。

不同域名下的 Cookie 各自独立，每当客户端发起请求时，会**自动**把**当前域名下**所有**未过期的 Cookie** 一同发送到服务器。

**Cookie的几大特性：**

① 自动发送

② 域名独立

③ 过期时限

④ 4KB 限制



###### **4. Cookie 在身份认证中的作用**

![](images\cookies.png)



###### **5. Cookie不具有安全性**

![](images\cookie2.png)

###### **6.** **提高身份认证的安全性**

![](images\cookie3.png)



###### **7.** **Session的工作原理**

![](images\session.png)





### **5. 在 Express 中使用 Session 认证**



##### **1.** **安装** **express-session 中间件**

在 Express 项目中，只需要安装 **express-session** 中间件，即可在项目中使用 Session 认证：

```
npm install express-session
```



##### **2.** **配置** **express-session 中间件**

express-session 中间件安装成功后，需要通过 **app.use()** 来注册 **session 中间件**，示例代码如下

```js
// 1. 导入 session 中间件
const session = require('express-session')

// 2. 配置 session 中间件
app.use(session({
    secret: 'keyboard cat',  // secret 属性的值可以为任意字符串
    resave: false,           // 固定写法
    saveUninitialized: true  // 固定写法
}))
```



##### **3. 向 session 中存数据**

当 **express-session** 中间件配置成功后，即可通过 **req.session** 来访问和使用 session 对象，从而存储用户的关键信息：

```js
app.post('.api/login' , (req,res) => {
    // 判断用户提交的登录信息是否正确
    if(req.body.username !== 'admin' || req.body.password !== '000000') {
        return res.send({ status: 1 , msg:'登陆失败' })
    }
    req.session.user = req.body  // 将用户的信息，存储到 session 中
    req.session.islogin = true   // 将用户的登录状态， 存储到 session 中
    
    res.send({ status: 0 , msg:'登录成功
})
```



##### **4. 从 session 中取数据**

可以直接从 **req.session** 对象上获取之前存储的数据，示例代码如下：

```js
// 获取用户姓名的接口
app.get('/api/username' , (req,res) => {
    // 判断用户是否登录
    if(!req.session.islogin) return res.send({ status: 1 , msg:'fail' })
    
    res.send( { status: 0 , msg: 'success' , username: req.session.user.username } )
})
```



##### **5. 清空 session**

调用 **req.session.destroy()** 函数，即可清空服务器保存的 session 信息。

```js
// 退出登录的接口
app.post('/api/logout' , (req,res) => {
    // 清空当前客户端对应的 session 信息
    req.session.destroy()
    res.send({
        status: 0,
        msg:'退出登录成功'
    })
})
```



##### **6. 了解 Session 认证的局限性**

Session 认证机制需要配合 Cookie 才能实现。由于 Cookie 默认不支持跨域访问，所以，当涉及到前端跨域请求后端接

口的时候，**需要做很多额外的配置**，才能实现跨域 Session 认证。

注意：

⚫ 当前端请求后端接口**不存在跨域问题**的时候，**推荐使用 Session** 身份认证机制。

⚫ 当前端需要跨域请求后端接口的时候，不推荐使用 Session 身份认证机制，推荐使用 JWT 认证机制。



### **6. JWT 认证机制**

JWT（英文全称：JSON Web Token）是目前**最流行**的**跨域认证解决方案**

##### **1. JWT 的工作原理**

![](images\jwt.png)



##### **2. JWT 的组成部分**

![](images\jwt1.png)



##### **3. JWT 的三个部分各自代表的含义**

![](images\jwt2.png)



##### **4. JWT 的使用方式**

![](images\jwt3.png)



###  **7. 在 Express 中使用 JWT**



##### **1.** **安装** **JWT 相关的包**

运行如下命令，安装如下两个 JWT 相关的包：

```
npm install jsonwebtoken express-jwt
```

其中：

⚫ **jsonwebtoken** 用于**生成 JWT 字符串**

⚫ **express-jwt** 用于**将 JWT 字符串解析还原成 JSON 对象**



##### **2.** **导入** **JWT 相关的包**

使用 **require()** 函数，分别导入 JWT 相关的两个包：

```js
// 1. 导入用于生成 jwt 字符串的包
const jwt = require('jsonwebtoken')

// 2. 导入用于将客户端发送过来的 jwt 字符串，解析还原成 json 对象的包
const expressJWT = require('express-jwt')
```



##### **3. 定义 secret 密钥**

为了**保证 JWT 字符串的安全性**，防止 JWT 字符串在网络传输过程中被别人破解，我们需要专门定义一个用于**加密**和**解密**

的 secret 密钥：

① 当生成 JWT 字符串的时候，需要使用 **secret 密钥**对用户的信息进行**加密**，最终得到加密好的 JWT 字符串

② 当把 JWT 字符串解析还原成 JSON 对象的时候，需要使用 secret 密钥**进行解密**

```js
// secret 密钥的本质： 就是一个字符串
const secretKey = 'xiaoheizi No1 ++_++'
```



##### **4. 在登录成功后生成 JWT 字符串**

调用 **jsonwebtoken** 包提供的 **sign()** 方法，**将用户的信息加密成 JWT 字符串**，**响应给客户端：**

```js
// 登录接口
app.post('/api/login' , function(req,res) => {
         // ...省略登录失败情况下的代码
         // 用户登录成功后， 生成 JWT 字符串， 通过 token 属性响应给客户端
         
         res.send({
            status: 200,
            message: '登录成功!',
            // 调用 jwt.sign() 生成 JWT 字符串， 三个参数分别是：用户信息对象、加密密钥、配置对象
            token: jwt.sign({ username: userinfo.username } , secretKey , { expiresIn : '30s'})
         })
})
```



##### **5. 将** **JWT 字符串还原为JSON 对象**

客户端每次在访问那些有权限接口的时候，都需要主动通过**请求头中的 Authorization 字段**，将 Token 字符串发

送到服务器进行身份认证。

此时，服务器可以通过 **express-jwt** 这个中间件，自动将客户端发送过来的 Token 解析还原成 JSON 对象：

```js
// 使用 app.use() 来注册中间件
// expressJWT({ secret: secretKey })  就是用来解析 Token 的中间件
// .unless({ path: [/^\/api\//] })  用来指定哪些接口不需要访问权限  /api开头的接口不需要访问权限
app.use(expressJWT({ secret: secretKey }).unless({ path: [/^\/api\//] }))
```



##### **6. 使用 req.user 获取用户信息**

当 express-jwt 这个中间件配置成功之后，即可在那些有权限的接口中，使用 **req.user** 对象，来访问从 JWT 字符串

中解析出来的用户信息了，示例代码如下：

```js
// 这是一个有权限的 api 接口
app.get('admin/getinfo' , function(req,res) {
    console.log(req.user)
    
    res.send({
        status: 200,
        message: '获取用户信息成功',
        data: req.user
    })
})
```



##### **7. 捕获解析 JWT 失败后产生的错误**

当使用 express-jwt 解析 Token 字符串时，如果客户端发送过来的 Token 字符串**过期**或**不合法**，会产生一个**解析失败**

的错误，影响项目的正常运行。我们可以通过 **Express 的错误中间件**，捕获这个错误并进行相关的处理，示例代码如下：

```js
app.use((err,req,res,next) => {
    // token 解析失败导致的错误
    if(err.name === 'UnauthorizedError') {
        return res.send({ status: 401 , message: '无效的token' })
    }
    // 其他原因导致的错误
    re.send({ status: 500, message: '未知错误' })
})
```



app.js

```js
// 导入 express 模块
const express = require('express')
// 创建 express 的服务器实例
const app = express()

// TODO_01：安装并导入 JWT 相关的两个包，分别是 jsonwebtoken 和 express-jwt
const jwt = require('jsonwebtoken')
const expressJWT = require('express-jwt')

// 允许跨域资源共享
const cors = require('cors')
app.use(cors())

// 解析 post 表单数据的中间件
const bodyParser = require('body-parser')
app.use(bodyParser.urlencoded({ extended: false }))

// TODO_02：定义 secret 密钥，建议将密钥命名为 secretKey
const secretKey = 'xiaoheizi No1 ++_++'

// TODO_04：注册将 JWT 字符串解析还原成 JSON 对象的中间件
// 注意： 只要这个express-jwt中间件配置成功之后，就可以把解析出来的用户信息，挂载到 req.user 属性上!!!!
// .unless({ path: [/^\/api\//] })  用来指定哪些接口不需要访问权限  /api开头的接口不需要访问权限
app.use(expressJWT({ secret: secretKey }).unless({ path: [/^\/api\//] }))

// 登录接口
app.post('/api/login', function(req, res) {
    // 将 req.body 请求体中的数据，转存为 userinfo 常量
    const userinfo = req.body
        // 登录失败
    if (userinfo.username !== 'admin' || userinfo.password !== '000000') {
        return res.send({
            status: 400,
            message: '登录失败！'
        })
    }
    // 登录成功
    // TODO_03：在登录成功之后，调用 jwt.sign() 方法生成 JWT 字符串。并通过 token 属性发送给客户端
    // 注意： 千万不要把密码加密到 token 字符串中
    // 调用 jwt.sign() 生成 JWT 字符串， 三个参数分别是：用户信息对象、加密密钥、配置对象,可以配置 token 有效期(30s后作废)
    const tokenStr = jwt.sign({ username: userinfo.username }, secretKey, { expiresIn: '300s' })
    res.send({
        status: 200,
        message: '登录成功！',
        // 要发送给客户端的 token 字符串
        token: tokenStr
    })
})

// 这是一个有权限的 API 接口
app.get('/admin/getinfo', function(req, res) {
    // TODO_05：使用 req.user 获取用户信息，并使用 data 属性将用户信息发送给客户端
    console.log(req.user)
    res.send({
        status: 200,
        message: '获取用户信息成功！',
        data: req.user // 要发送给客户端的用户信息
    })
})

// 项目最后注册错误的中间件
// TODO_06：使用全局错误处理中间件，捕获解析 JWT 失败后产生的错误
app.use((err, req, res, next) => {
    // token 解析失败导致的错误
    if (err.name === 'UnauthorizedError') {
        return res.send({ status: 401, message: '无效的token' })
    }
    // 其他原因导致的错误
    re.send({ status: 500, message: '未知错误' })
})

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(8888, function() {
    console.log('Express server running at http://127.0.0.1:8888')
})
```

![](images\token1.png)

![](images\token2.png)





