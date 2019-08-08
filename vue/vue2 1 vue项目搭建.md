



1. 基础篇: Vue核心知识点
2. 生态篇: 大型Vue项目所需的周边技术
3. 实战篇: 开发基于Vue的Ant Design Pro

## Vue.component缺点

+ 全局定义: 强制要求每个component中的命名不得重复
+ 字符串模板: 缺乏语法高亮
+ 不支持CSS
+ 没有构建步骤



### Vue CLI 使用步骤

+ npm install -g @vue/cli
+ vue create hello-world 
+ cd hello-world
+ npm run server

### Vue组件  三大核心组件

#### 属性

+ 自定义属性: props, 组件props中声明的属性
+ 原生属性: attrs  没有声明的属性, 默认自动挂在到组件根元素上, 设置inheritAttrs为false可以关闭自动挂载
+ 特殊属性: class style  挂载到组件根元素上, 支持字符串, 对象, 数组等多种语法

#### 事件

+ 普通事件
+ 修饰符事件

this.$emit的返回值是什么?

#### 插槽 (slot)

+ 普通插槽
+ 作用域插槽

相同名称的插槽是合并还是替换? 2.5 2.6



### 虚拟Dom及Key属性的作用



### 触发组件的更新

 #### 状态 data V.S 属性 props

+ 状态是组件自身的数据
+ 属性是来自父组件的数据
+ 状态的改变未必会触发更新
+ 属性的改变未必会触发更新

数组有哪些方式支持响应式更新? 如果不支持如何处理, 底层原理如何实现?



### 计算属性, 监听器

#### 计算属性 (computed)

+ 减少模板中计算逻辑
+ 数据缓存
+ 依赖固定的数据类型(响应式数据)

#### 侦听器 (watch)

 

#### computed vs watch

+ computed能做的, watch都能做, 反之则不行
+ 能用computed的尽量用computed

### Vue组件的生命周期

1. 创建阶段
2. 更新阶段
3. 销毁阶段

### 函数式组件

+ functional: true
+ 无状态 无实例 没有this上下文 无生命周期



### Vue指令

内置14中vue指令.  语法糖, 标志位

自定义指令 

 

### Vue中的常用高级特性: provide inject



### 跨层级组件实例





### template和JSX的对比以及他们的本质





## 生态篇

### Vuex



























