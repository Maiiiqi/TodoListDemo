<template>
  <div id="app">
    <div class="todo-container">
      <div class="todo-wrap">
        <TodoHeader :addTodo="addTodo"/>
        <TodoList 
          :todos="todos" 
          :changeState="changeState"
          :removeTodo="removeTodo"
        />
        <TodoFooter
          :todos="todos"
          :setAll="setAll"
          :removeDoneTodo="removeDoneTodo"
        />
      </div>
    </div>
  </div>
</template>

<script>
import TodoHeader from './components/TodoHeader'
import TodoList from './components/TodoList'
import TodoFooter from './components/TodoFooter'
export default {
  name: 'App',
  components: {TodoHeader, TodoList, TodoFooter},
  data() {
    return {
      todos: [
        {id: '001', title: '吃饭', done: true},
        {id: '002', title: '睡觉', done: true},
        {id: '003', title: '打豆豆', done: false}
      ]
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
    }
  },
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
