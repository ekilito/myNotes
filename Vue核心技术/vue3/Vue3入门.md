# Vue3
## 1. Vue3组合式API体验
```vue
<script>
// vue2 的代码
export default {
  data(){
    return {
      count:0
    }
  },
  methods:{
    addCount(){
      this.count++
    }
  }
}
</script>
```

```vue
<script setup>
// vue3 组合式api的实现
import { ref } from 'vue'
const count = ref(0)
const addCount = ()=> count.value++
</script>
```

特点：

1. 代码量变少
2. 分散式维护变成集中式维护
## 2. Vue3更多的优势
![image.png](assets/01.png)



# 使用create-vue搭建Vue3项目

## 1. 认识create-vue
> create-vue是Vue官方新的脚手架工具，底层切换到了 vite （下一代前端工具链），为开发提供极速响应


![image.png](assets/2.png)

## 2. 使用create-vue创建项目
> 前置条件 - 已安装16.0或更高版本的Node.js

执行如下命令，这一指令将会安装并执行 create-vue
```bash
npm init vue@latest
```

![image.png](assets/3.png)



# 熟悉项目和关键文件

![image.png](assets/4.png)

```js
// main.js 入口文件
import './assets/main.css'

// new Vue() 创建一个应用实例对象

import { createApp } from 'vue'
import App from './App.vue'

// 1. 以 App 作为参数生成一个应用实例对象
// 2. 挂载到id为app的节点上
createApp(App).mount('#app')
```



# setup选项

## 1. setup选项的写法和执行时机
写法
```vue
<script>
  export default {
    setup(){
      // setup 在 beforeCreate 钩子之前自动执行
    },
    beforeCreate(){
      
    }
  }
</script>
```
执行时机
> 在beforeCreate钩子之前执行

![image.png](assets/5.png)

## 2. setup中写代码的特点
> 在setup函数中写的数据和方法需要在末尾以对象的方式return，才能给模版使用

```vue
<script>
export default {
  setup() {
     console.log('setup', this);  // this 指向 undefined, 没有 this 组件实例

     const message = 'this is message'

     const logMessage = () => console.log(message)

     // 必须 return 才能再模板中使用 (使用 setup 语法糖就不用导出)
     return { message, logMessage }
  },

  beforeCreate () {
    console.log('beforeCreate')
  }
}
</script>

<template>
  <div>{{ message }}</div>
</template>
```
## 3. **<script setup>**语法糖
> **script标签添加 setup标记，不需要再写导出语句，默认会添加导出语句**
>
> 开启语法糖
> 好处：1. 不需要在编写export default{} 2. 不需要再return让模板使用数据
>
> 把原本比较复杂的语法经过编译环境变成简单的语法 开发的体验更好 但是实际在浏览器中运行的还是原本的语法

```vue
<script setup>
  const message = 'this is message'
  const logMessage = ()=>{
    console.log(message)
  }
</script>
```



# reactive和ref函数

## 1. reactive
> 作用：接受**对象类型**数据的参数传入， 并返回一个响应式的对象
>
> reactive 函数，可以转换对象和数组为响应式数据
>
> vue3 实现响应式数据就是 proxy
>
> 通常定义：复杂类型的响应式数据
>
> 不能转换简单数据


```vue
<script setup>
 // 1. 导入 reactive 函数
 import { reactive } from 'vue'
    
 // 2. 执行函数 传入一个对象类型参数 变量接收
 const state = reactive({
   count: 0
 })
 
 const setCount = ()=>{
   // 修改数据更新视图
   state.count++
 }
 
</script>

<template>
     <div>{{ count }}<button @click="setCount"></button></div>
</template>
```

## 2. ref
> 接收简单类型或者对象类型的数据传入并，返回一个响应式的对象

```vue
<script setup>
 // 1. 导入 ref 函数
 import { ref } from 'vue'

 // 2. 执行函数 传入参数[简单类型 + 对象类型] 变量接收

 const count = ref(0)

 const setCount = () => {
  // 脚本区域修改 ref 产生的响应式对象数据  必须通过 .value 属性
  count.value++
 }
</script>

<template>
     <button @click="setCount">{{ count }}</button>
</template>
```
## 3. reactive 对比 ref

1. **都是用来生成响应式数据**
2. 不同点
   1. **reactive不能处理简单类型的数据**
   2. **ref参数类型支持更好，但是必须通过.value做访问修改**
   3. ref函数内部的实现依赖于reactive函数
3. 在实际工作中的推荐
   1. **推荐使用ref函数，减少记忆负担**
   2. 如果能确定数据是对象且字段名也确定，用 reactive



# computed

> 计算属性基本思想和Vue2保持一致，组合式API下的计算属性只是修改了API写法
>
> 使用 computed 函数，传入一个函数，函数返回计算好的数据。
>
> 最后setup函数返回一个对象，包含该计算属性的数据即可。
>
> 当需要依赖一个数据得到新的数据时使用计算属性

```vue
<script setup>
    
// 1. 导入 computed 函数
import {ref, computed } from 'vue'
    
// 原始数据
const list = ref([1,2,3,4,5,6,7,8])

// 2. 执行函数 return计算之后的值 变量接收
const filterList = computed(() => {
    // 做计算 根据一个数据计算得到一个新的数据
    return list.value.filter(item => item > 2)
})

setTimeout(() => {
    list.value.push(9,10)
},3000)

/*
   1. 计算属性中不应该有'副作用'    比如异步请求/修改dom
   2. 避免直接修改计算属性的值      计算属性应该是只读的
   
*/
</script>
```


# watch

> 侦听一个或者多个数据的变化，数据变化时执行回调函数(支持副作用 ajax请求 dom操作)
>
> 俩个额外参数 :     
>
> **immediate** 控制立刻执行
>
> **deep**   开启深度侦听 主要针对于嵌套层次比较深的对对象

## 1. 侦听单个数据
```vue
<script setup>
    
// 1. 导入 watch
import {ref , watch} from 'vue'
const count = ref(0)
const setCount = () => {
  count.value++
}

// 2. 调用 watch 侦听变化
// 参数1: 监听哪个数据就把它放过来 ref对象不需要加 .value 
// 参数2：监听的数据发生变化时要执行的回调函数 
watch(count,(newVal,oldVal) => {
   console.log('count变化了',newVal,oldVal)
})
    
</script>

<template>
   <dev><button @click="setCount">+{{ count }}</button></dev>
</template>
    
</script>
```
## 2. 侦听多个数据
> 侦听多个数据，第一个参数可以改写成数组的写法

```vue
<script setup>
  // 1. 导入 watch
import {ref , watch} from 'vue'
    
const count = ref(0)
const changeCount = () => {
  count.value++
}

const name = ref('cp')
const changeName = () => {
  name.value = 'pc'
}

// 2. 调用 watch 侦听变化
watch(
  [count,name],
  (
    [newCount,newName],
    [oldCount,oldName]
  ) => {
     console.log('count / name 变化了',[newCount,newName],[oldCount,oldName]);
})

</script>

<template>
   <dev><button @click="changeCount">{{ count }}</button></dev>
   <dev><button @click="changeName">{{ name }}</button></dev>
</template>
```
## 3. immediate
> 在侦听器创建时立即出发回调，响应式数据变化之后继续执行回调
>
> 立即执行：初始化的时候 回调先执行一次 等到数据变化函数再次执行


```vue
<script setup>
    
  // 1. 导入watch
  import { ref, watch } from 'vue'
  const count = ref(0)
  
  // 2. 调用watch 侦听变化
  watch(count, (newValue, oldValue)=>{
     console.log('count变化了',newVal,oldVal)
  },{
    immediate: true
  })
    
</script>
```
## 4. deep
> 通过watch监听的ref对象默认是浅层侦听的，直接修改嵌套的对象属性不会触发回调执行，需要开启deep
>
> 问题：
>
> deep为true Vue针对于传入的对象进行递归处理 如果要处理的对象非常大 会有性能问题
>
> 尽量少用deep 只有在要监听的属性并不确定在那一层 或者要监听的属性有很多个分布在不同的对象层次里

```vue
<script setup>
    
  // 1. 导入watch
  import { ref, watch } from 'vue'  
  const state = ref({ count: 0 })
  // 2. 监听对象state
  watch(state, ()=>{
    console.log('数据变化了')
  }) 
  const changeStateByCount = ()=>{
    // 直接修改不会引发回调执行
    state.value.count++
  } 
</script>


<script setup>
    
  // 1. 导入watch
  import { ref, watch } from 'vue'
    
  const state = ref({ count: 0 })
  
  // 2. 监听对象state 并开启deep
  watch(state, ()=>{
    console.log('数据变化了')
  },{
      deep:true  // 如果开启了 deep, 不管层次嵌套有多深，都会做递归处理
  })
    
  const changeStateByCount = ()=>{
    // 此时修改可以触发回调
    state.value.count++
  }
  
</script>




<script setup>
    
  // 1. 导入watch
  import { ref, watch } from 'vue'
    
  const state = ref({ 
      name: 'xiaobai',
      age: 18
  })
  
   const changeName = ()=>{
    state.value.name = 'xiaohei'
  }
   
   const changeAge = () => {
       state.value.age = 20
   }
   // 精确侦听某个具体属性
   watch(
      () => state.value.age,   // 监听 age
      () => {
          console.log('age变化了')
      }
   )
    
 
  
</script>

```

# 生命周期函数

> 1. 什么是生命周期？从组件创建到销毁的各个阶段 时机到了就自动执行的函数
> 2. 分阶段说明各个阶段都运行哪些函数？
>    1.  初始化  beforeCreate - created - beforeMount - mounted
>    2. 更新阶段 beforeUpdate - updated (只要数据变化 按照顺序重复执行)
>    3. 销毁时 beforeDestory - destoryed (销毁时按照顺序执行一次)
> 3. 业务高频使用的几个函数
>    1. created -m ajax
>    2. mounted - ajax + 和dom元素相关的初始化操作（echarts图表 + 地图图表）
>    3. beforeDestory - 释放内存（清理定时器 clearInterval(timeId) + 清理全局绑定的高频时间window scroll）
> 4. keep-alive
>    1. 缓存组件 activated(当前缓存组件被激活时触发)
>    2. deactivated（当前缓存组件失活时触发）

## 1. 选项式对比组合式

> 组合式API的生命周期
>
> on + 生命周期函数 = 组合式API下调用的函数 onMounted( () => {  } )

![image.png](assets/6.png)
## 2. 生命周期函数基本使用
> 1. 导入生命周期函数
> 2. 执行生命周期函数，传入回调

```js
<scirpt setup>
    // 1. 引入函数
import { onMounted } from 'vue'

    // 2. 执行函数 传入回调
onMounted(()=>{
  // 自定义逻辑
  console.log('组件挂载完毕mounted执行了')
})
</script>
```
## 3. 执行多次
> 生命周期函数执行多次的时候，会按照顺序依次执行
>
> 本质：每次调用onMounted往Vue里添加回调函数 mountedList:[cb1, cb2] 等待组件渲染完毕dom可用 实际成熟 使用forEach遍历mountedList 依次执行里面的回调
>
> onMounted函数本身不会等待组件渲染完毕才执行  组件初始化的时候就执行  传入的回调函数cb 才会等到dom可用时在执行

```js
<scirpt setup>
    
import { onMounted } from 'vue'

// 生命周期是可以执行多次的，多次执行时传入的回调会在时机成熟时依次执行
onMounted(()=>{
  // 自定义逻辑
})

onMounted(()=>{
  // 自定义逻辑
})
</script>
```


#  父子通信

## 1. 父传子
> 基本思想
> 1. 父组件中给子组件标签上绑定属性  <Son :name="name" />
> 2. 子组件内部通过props选项接收数据  props: { name: { type: String } }
>
> 注意：
>
> 1. 如果使用 defineProps 接收数据，这个数据只能在模板中渲染
> 2. 如果想要在 js 中页操作接收的数据，应接收返回值来使用。


![image.png](assets/7.png)

## 2. 子传父
> 基本思想
> 1. 父组件中给子组件标签通过@绑定自定义事件，等于号绑定一个父组件中的函数。父组件提供方法。<SonCom @get-title="getTitle" />
> 2. 子组件内部通过 defineEmit 获取 emit 函数（因为没有this）  const emit = defineEmits('[ get-title ]')
> 3. 子组件通过 emit 触发事件，并且传递数据 emit('get-title', 'this is title')
>
> 本质：在子组件中调用了父组件中的方法
>
> 流程：emit触发事件 - 通过自定义事件名找到父组件中的函数 - 触发这个函数
>
> 可不可以直接把一个函数通过父传子传过去 :http-request="upload"


![image.png](assets/8.png)



# 模版引用

> 概念：通过 ref标识 获取真实的 dom对象或者组件实例对象
>
> this.$refs.form.validate()
>
> 1. 通过ref标识拿到当前form组件实例对象
> 2. 调用了它内部的方法 validate

## 1. 基本使用
> 实现步骤：
> 1. 调用ref函数生成一个ref对象  ref(null) -> divRef
> 2. 通过ref标识绑定ref对象到标签  <div ref="divRef"></div>
> 3. 组件挂载完毕之后才能获取 onMounted(()=> { console.log(divRef.value ) } )

![image.png](assets/9.png)
## 2. defineExpose
> 默认情况下在 <script setup>语法糖下组件内部的属性和方法是不开放给父组件访问的，可以通过defineExpose编译宏指定哪些属性和方法容许访问
> 说明：指定testMessage属性可以被访问到

![image.png](assets/10.png)



# provide和inject

## 1. 作用和场景
> 顶层组件向任意的底层组件传递数据和方法，实现跨层组件通信

![image.png](assets/11.png)

## 2. 跨层传递普通数据
> 实现步骤
> 1. 顶层组件通过 `provide` 函数提供数据
> 2. 底层组件通过 `inject` 函数获取数据


![image.png](assets/12.png)

## 3. 跨层传递响应式数据
> 在调用provide函数时，第二个参数设置为ref对象

![image.png](assets/13.png)

## 4. 跨层传递方法
> 顶层组件可以向底层组件传递方法，底层组件调用方法修改顶层组件的数据

![image.png](assets/14.png)

```vue
// 通过传递函数实现底层组件修改顶层组件中的数据

const count = ref(100)

const setCount = (newCount) => {
   count.value = newCount
}

provide('count-key', { count,setCount })


// 子组件

const countOBJ = inject('count-key')
// console.log(countOBJ)  // { count: refimpl , setCount: f }

const changeCount = () => {
   countOBJ.setCount(101)
}

{{ countOBJ.count }}
<button @click=changeCount>修改count</button>
```

**思想**

1. 在应用组件树中是不是只有一个顶层和一个底层？相对概念 存在很多个顶层和底层 只有存在这样的关系就可以使用这对API
2. 这对API只在小范围内跨层组件传值时才使用， 传统的父子通信就还用父子通信就可以了
3. APP（provide('key')）- Father(provide('key')) - Son(inject('key'))    取值遵守就近原则
4. provide+inject 成对使用的 假如一个组件中使用了inject  限制了组件的上游必须提供一个provide 这个组件不够通用 不i你在组件树的任意位置放置



# toRefs

> 掌握：在使用reactive创建的响应式数据被展开或解构的时候使用toRefs保持响应式
>
> - 解构响应式数据，踩坑
> - 使用 `toRefs` 处理响应式数据，爬坑
> - `toRefs` 函数的作用，与使用场景



```vue
<script setup>
import { reactive } from "vue";
const user = reactive({ name: "tom", age: 18 });
</script>

<template>
  <div>
    <p>姓名：{{ user.name }}</p>
    <p>年龄：{{ user.age }} <button @click="user.age++">一年又一年</button></p>
  </div>
</template>

```

- 使用响应式数据，踩坑

```vue
<script setup>
import { reactive } from "vue";
const { name, age } = reactive({ name: "tom", age: 18 });    // 丢失响应式
</script>

<template>
  <div>
    <p>姓名：{{ name }}</p>
    <!-- 响应式丢失 -->
    <p>年龄：{{ age }} <button @click="age++">一年又一年</button></p>
  </div>
</template>

```

- 使用 `toRefs` 处理响应式数据，爬坑

```js
import { reactive, toRefs } from "vue";

const user = reactive({ name: "tom", age: 18 });

const { name, age } = toRefs(user)

```

`toRefs` 函数的作用，与使用场景

- 作用：把对象中的每一个属性做一次包装成为响应式数据
- 响应式数据展开的时候使用，解构响应式数据的时候使用

**总结：**

- 当去解构和展开响应式数据对象使用 `toRefs` 保持响应式