<template>
  <div id="app">
    <div class="todo-container">
      <div class="todo-wrap">
        <!-- 采用props的方式接收子组件的数据 -->
        <!-- <TodoHeader :addTodo="addTodo"/> -->
        <!-- 采用自定义事件 -->
        <TodoHeader @addEvent="addTodo"/>

        <!-- props -->
        <!-- <TodoList 
          :todos="todos" 
          :changeState="changeState"
          :removeTodo="removeTodo"
        /> -->
        <!-- 采用全局事件总线 -->
        <TodoList :todos="todos"/>

        <!-- props -->
        <!-- <TodoFooter
          :todos="todos"
          :setAll="setAll"
          :removeDoneTodo="removeDoneTodo"
        /> -->
        <!-- 自定义事件 -->
        <TodoFooter
        :todos="todos"
        @setAll="setAll"
        @removeDoneTodo="removeDoneTodo"
        />
      </div>
    </div>
  </div>
</template>

<script>
import TodoHeader from './components/TodoHeader'
import TodoList from './components/TodoList'
import TodoFooter from './components/TodoFooter'
import pubsub from 'pubsub-js'
export default {
  name: 'App',
  components: {TodoHeader, TodoList, TodoFooter},
  data() {
    return {
      //读取localStorage里的数据并转换成JSON对象
      todos: JSON.parse(window.localStorage.getItem('todos')) || []   //当localStorage里没数据时，返回值为null，在Footer组件中使用到了todos.length，而null没有length属性，所以此处要||一个空数组[]
      // []的length为0
    }
  },
  methods: {
    //将todo对象放入todos数据中
    addTodo(todo){
      this.todos.unshift(todo)
    },
    //根据id修改对应todo对象的done字段
    changeState(id){
      this.todos.forEach((todo)=>{
        if (todo.id == id) 
          todo.done = !todo.done
      })
    },
    //根据id将对应的todo对象从列表中删除
    removeTodo(id){
      this.todos = this.todos.filter(todo=> todo.id !== id)
    },
    //根据Footer中checkbox的状态对todos的所有元素的状态进行修改
    setAll(state){
      this.todos.forEach((todo)=> todo.done = state)
    },
    //清除所有已完成的任务
    removeDoneTodo(){
      this.todos = this.todos.filter(todo=>!todo.done)
    },
    //更新todo对象内容
    updateItem(id, title){
      this.todos.forEach((todo)=>{
        if (todo.id == id)  todo.title = title
      })
    }
  },
  //监视todos的值，一旦发生变化就往localStorage里存储，实现数据同步
  watch: {
    todos: {
      //todos是一个对象数组，对象内部的属性Vue默认监测不到，需要采用深度监视
      deep: true,
      handler(newValue){
        window.localStorage.setItem('todos', JSON.stringify(newValue)) //setItem存入的是string类型的数据，需要stringify一下，否则存入的就只是一个简单的object
      }
    }
  },
  //在当前组件挂载后为$bus绑定事件和回调
  //回调函数要留在想要接收数据的组件中
  mounted(){
    //全局事件总线——通过vc实例(此处的this)访问Vue原型对象上的$bus对象
    this.$bus.$on('itemChange', this.changeState)
    //this.$bus.$on('itemRemove', this.removeTodo)
    this.$bus.$on('itemUpdate', this.updateItem)

    //or 通过消息订阅与发布实现remove功能
    this.pid = pubsub.subscribe('removeMsg', (msgName, id)=>{ //为了让beforeDestroy也访问到pid字段，可以把它放在this——vc实例上
      this.todos = this.todos.filter(todo=> todo.id !== id)  //此处的回调写成箭头函数，这样函数内的this也是指向vc实例。箭头函数接收两个参数，第一个为消息名，第二个才是传递过来的数据
    })  //订阅消息
  },
  //在使用总线事件的组件销毁前解绑事件
  beforeDestroy(){
    //解绑事件
    this.$bus.$off('itemChange')
    //this.$bus.$off('itemRemove')
    //取消订阅
    pubsub.unsubscribe(this.pid)
  }
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
  body{
    background: #fff;
  }
  .btn{
    display: inline-block;
    padding: 4px 12px;
    margin-bottom: 0;
    font-size: 14px;
    line-height: 20px;
    text-align: center;
    vertical-align: middle;
    cursor: pointer;
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  }
  .btn-edit{
    color: #fff;
    background-color: rgb(126, 209, 241);
    border: 1px solid rgb(90, 133, 150);
    margin-right: 5px;
  }
  .btn-edit:hover{
    color: #fff;
    background-color: rgb(85, 175, 211);
  }
  .btn-danger{
    color: #fff;
    background-color: #da4f49;
    border: 1px solid #bd362f;
  }
  .btn-danger:hover{
    color: #fff;
    background-color: #bd362f;
  }
  .btn:focus{
    outline: none;
  }
  .todo-container{
    width: 600px;
    margin: 0 auto;
  }
  .todo-container .todo-wrap{
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
  }
</style>
