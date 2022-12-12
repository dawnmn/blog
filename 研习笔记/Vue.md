
常用生命周期钩子函数：created、mounted、updated、beforeDestroy
生命周期四大阶段：
1.初始化阶段:
beforeCreate：实例刚创建完成,此时还没有data、methods、props属性
created：vue实例data、methods、props属性已经初始化完成，可以做一些初始数据的获取，此时还没有编译模板

2.实例挂载阶段
beforeMount：挂载前模板编译完成，此时el还没有挂载，可以在当前阶段进行更改数据，不会造成重渲染。
mounted：真实的Dom挂载完毕，数据完成双向绑定。

3.数据更新阶段
beforeUpdate： 数据更新时执行,data数据此时已经是最新的数据,UI界面还是旧的
updated：数据更新完成后,界面和data里的数据此时都是最新的。

4.销毁阶段
beforeDestroy： 销毁前，实例准备销毁,此时data和methods方法都能用，可以在这时进行善后收尾工作
destroyed： 销毁后，实例销毁完成,此时原先创建的实例方法和属性都不可用

模板语法：指令 参数 事件 修饰符
计算属性（computed）：基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。
侦听器（watch）：当一个数据变化时，执行一个函数。
事件修饰符：.stop .prevent .capture .self .once .passive
按键修饰符：.enter .tab .delete .esc .space .up .down .left .right
@keyup.按键修饰符="事件处理函数" -当按键抬起时触发；
@keydown.按键修饰符="事件处理函数" -当按键按下时触发；
表单输入双向数据绑定：v-model= v-model.trim=

组件：
因为组件是可复用的Vue实例，所以它们与new Vue接收相同的选项，例如data、computed、watch、methods以及生命周期钩子等。仅有的例外是像el这样根实例特有的选项。
监听子组件事件：父组件 @event1=""，子组件 @event2="$emit('event1')"    $emit触发当前实例上的事件
组件注册：局部注册、全局注册
Prop:所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定
组件属性(property )：this.$el data

路由库：router
状态库：Vuex
promise
axios

vue与jquery区别：
jquery是使用选择器选取DOM对象，对其进行赋值、取值、事件绑定等操作。
vue则是利用组件进行双向数据绑定操作数据，利用组件的函数处理事件。