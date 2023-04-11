# Vue3 + TS开发环境创建
## 1. 创建环境
vite除了支持基础阶段的纯TS环境之外，还支持 Vue + TS开发环境的快速创建, 命令如下：
```bash
$ npm create vite@latest  vue-ts-pro -- --template vue-ts 
```

说明：

1. npm create vite[@latest ](/latest )   基于最新版本的vite进行项目创建 
2. vue-ts-pro    项目名称
3. -- --template vue-ts    选择Vue + TS的开发模板

## 2. 和.vue文件相关的工具
**开发阶段**

1. Volar工具对.vue文件进行实时的类型错误反馈
2. TypeScript Vue Plugin 工具用于支持在 TS 中 import *.vue 文件

**打包阶段**
  vue-tsc工具负责打包时最终的类型检查
![image.png](assets/1.png)
好处：开发阶段的类型提示交给IDE做，保证vite的运行速度，打包阶段做’兜底类型校验’，确保类型无误

# 为Ref标注类型
## 1. 场景和好处
为ref标注类型之后，既可以在给ref对象的value赋值时校验数据类型，同时在使用value的时候可以获得代码提示
![9.png](assets/2.png)
说明：本质上是给ref对象的value属性添加类型约束

## 2. 如何标注
ref 函数和 TS 的配合通常分为俩种情况，类型推导和泛型指定类型
1. 如果是简单的数据，推荐使用类型推导
![11.png](assets/3.png)
2. 如果是较复杂的数据，通过泛型指定类型
![12.png](assets/4.png)

## 3. 思考题
标注ref函数类型，可以满足把下图所示的数据赋值给value属性
![16.png](assets/5.png)

# 为事件处理函数标注类型
## 1. 为什么需要标注类型
原生dom事件处理函数的参数默认会自动标注为any类型，没有任何类型提示，为了获得良好的类型提示，需要手动标注类型
![1.png](assets/6.png)

## 2. 如何标注类型
1. 给事件对象形参 e 标注为Event类型，可以获得事件对象的相关类型提示
![3.png](assets/7.png)
2. 如果需要更加精确的DOM类型提示可以使用断言（as）进行操作
![12.png](assets/8.png)
# 为模版引用标注类型
## 1. 为什么需要类型标注
给模版引用标注类型，本质上是给ref对象的value属性添加了类型约束，约定value属性中存放的是特定类型的DOM对象，从而在使用的时候获得相应的代码提示

![4.png](assets/9.png)
## 2. 如何进行类型标注
通过具体的DOM类型联合null做为泛型参数, 比如我们想获取一个input dom元素
![5.png](assets/10.png)

## 3. 思考题
尝试为模版引用标注类型获取一个a元素？
# 对象的非空值处理
## 1. 空值场景说明
当对象的属性可能是 null 或 undefined 的时候，称之为“空值”，尝试访问空值身上的属性或者方法会发生类型错误
![6.png](assets/11.png)
说明：inputRef变量在组件挂载完毕之前，value属性中存放的值为null，不是input dom对象，通不过类型校验

## 2. 可选链方案
可选链 ?. 是一种访问嵌套对象属性的安全的方式, 可选链前面的值为 undefined 或者 null时，它会停止运算
![1.png](assets/12.png)

## 3. 逻辑判断方案
通过逻辑判断，只有有值的时候才继续执行后面的属性访问语句
![2.png](assets/13.png)

## 4. 非空断言方案
非空断言（!）是指我们开发者明确知道当前的值一定不是null或者undefined，让TS通过类型校验
![5.png](assets/14.png)
**特别注意：**使用非空断言要格外小心，它没有实际的JS判断逻辑，只是通过了TS的类型校验，容易直接把空值出现在实际的执行环境里

# 为组件的Props标注类型
## 1. 为什么给Props标注类型
给组件的Props标注类型有俩个作用，一个是确保传递的prop是类型安全的，另一个在组件内部使用的时候也会有类型提示
![1.png](assets/15.png)

![4.png](assets/16.png)

## 2. 基础使用
语法：通过defineProps宏函数对组件props进行类型标注
需求：按钮组件有俩个prop参数，color类型为string且为必填，size类型为string且为可选，怎么定义类型？
![5.png](assets/17.png)
说明：按钮组件传递prop属性的时候必须满足color是必传项且类型为string, size为可选属性，类型为string

## 3. Props默认值设置
场景：Props中的可选参数通常除了指定类型之外还需要提供默认值，可以使用withDefaults宏函数来进行设置
需求：按钮组件的size属性的默认值设置为 middle
![7.png](assets/18.png)
说明：如果用户传递了size属性，按照传递的数据来，如果没有传递，则size值为 ’middle’

## 4. 思考题
给按钮组件添加一个btnType属性，类型为 ’success‘, ‘danger’ 或者 ’warning‘ 三选一, 默认值为 ’success‘
# 为组件的emits标注类型
## 1. 为什么需要标注类型
![9.png](assets/19.png)
作用：可以约束事件名称并给出自动提示，确保不会拼写错误，同时约束传参类型，不会发生参数类型错误

## 2. 何为组件的emits标注类型
语法：通过 defineEmits 宏函数进行类型标注
需求：子组件触发一个名称为 ’get-msg‘ 的事件，并且传递一个类型为string的参数
![8.png](assets/20.png)

## 3. 思考题
Son组件再触发一个事件’get-list’, 传递参数类型为下图所示的数据类型
![11.png](assets/21.png)

# 类型声明文件
## 1. 什么是类型声明文件
概念：在TS中以d.ts为后缀的文件就是类型声明文件，主要作用是为js模块提供类型信息支持，从而获得类型提示
![image.png](assets/22.png)

说明：

1. d.ts是如何生效的？
在使用js某些模块的时候，TS会自动导入模块对应的d.ts文件，以提供类型提示
2. d.ts是怎么来的？
库如果本身是使用TS编写的，在打包的时候经过配置自动生成对应的d.ts文件（axios本身就是TS编写的）

## 2. 使用 DefinitelyTyped 提供类型声明文件
场景：有些库本身并不是采用TS编写的，无法直接生成配套的d.ts文件，但是也想获得类型提示，此时需要 Definitely Typed 提供类型声明文件
![image.png](assets/23.png)

DefinitelyTyped是一个TS类型定义的仓库，专门为JS编写的库可以提供类型声明，比如可以安装 @types/jquery 为jquery提供类型提示
![image.png](assets/24.png)

## 3. TS内置类型声明文件
TS为JS运行时可用的所有标准化内置API都提供了声明文件，这些文件既不需要编译生成，也不需要三方提供

![image.png](assets/25.png)

说明：这里的lib.es5.d.ts以及lib.dom.d.ts都是内置的类型声明文件，为原生js和浏览器API提供类型提示
## 4. 自定义类型声明文件
d.ts文件在项目中是可以进行自定义创建的，通常有俩种作用，第一个是共享TS类型（重要），第二种是给js文件提供类型（了解）

**场景一：共享TS类型**

![image.png](assets/26.png)

说明：哪个业务组件需要用到类型导入即可，为了区分普通模块，可以加上type关键词

**场景二：给JS文件提供类型**
![image.png](assets/27.png)

说明：通过declare关键词可以为js文件中的变量声明对应类型，这样js导出的模块在使用的时候也会获得类型提示

## 5. .ts文件和d.ts文件对比
TS中有俩种文件类型，一种是.ts文件，一种是.d.ts文件
.ts文件

1. 既可以包含类型信息也可以写逻辑代码
2. 可以被编译为js文件

.d.ts文件

1. 只能包含类型信息不可以写逻辑代码
2. 不会被编译为js文件，仅做类型校验检查
# 综合案例
![image.png](assets/28.png)

## 1. 克隆项目
项目的初始化配置已经完成，其中包括：

1. 基于Vite搭建的基础的 Vue3 + TS 开发环境
2. 请求库 axios
3. 移动端组件库Vant
3. 静态结构组件模板

以下是项目地址，直接克隆项目安装依赖并run起来
```bash
git  clone http://git.itcast.cn/heimaqianduan/vue3-ts.git
```


## 2. 频道列表渲染 - 类型思想转变
之前业务开发我们用的是JavaScript，现在要加上TypeScript的类型，该如何把类型加进来呢？
![image.png](assets/29.png)

核心思想：使用TS之后的业务开发思想是保持一致的，重要的是根据接口格式定义响应式数据的类型以及axios返回数据的类型即可

## 3. 频道列表渲染 - 定义axios的返回数据类型
定义axios的返回数据类型需要配合一个axios的request方法通过泛型指定 

![image.png](assets/30.png)

![image.png](https://cdn.nlark.com/yuque/0/2023/png/274425/1680087381288-1d8dcdc9-7e17-4e70-bda4-e3fcc97421fc.png#averageHue=%239f9a66&clientId=u84bbf5d8-8668-4&from=paste&height=333&id=u68a5fca3&name=image.png&originHeight=666&originWidth=2576&originalType=binary&ratio=2&rotation=0&showTitle=false&size=220375&status=done&style=none&taskId=ua1b932ed-2125-49aa-900e-a78e1959f85&title=&width=1288)

说明: 我们通过泛型参数传给request方法的ChannelRes类型约束了axios返回值res的data属性的类型
## 4. 文章列表渲染
基础文章列表实现（固定频道id）

1. 根据接口格式定义数据类型
2. 使用 ref 函数定义响应式数据
3. 使用 axios 请求数据并赋值给响应式数据
4. 数据绑定到模版显示

频道和文章列表联动实现（切换不同的频道id）

1. 为List组件定义 props 类型
2. 传递当前频道的 id，使用当前id获取列表











