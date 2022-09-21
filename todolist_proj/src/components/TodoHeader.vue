<template>
  <div class="todo-header">
    <input type="text" placeholder="请输入要保存的代办事项，按回车键确认" v-model="userTodo" @keyup.enter="add"/>
  </div>
</template>

<script>
import {nanoid} from 'nanoid'  //nanoid是一个从npm中下来的库，提供nanoid()函数用于生成一个uuid唯一标识
export default {
  name: 'TodoHeader',
  methods: {
    add(event){
      //判断输入是否为空
      if (!this.userTodo.trim()) {alert('输入不能为空！'); return;}
      //根据用户输入创建todo对象
      const todoObj = {id: nanoid(), title: event.target.value, done: false}
      //将Todo对象传给父元素
      //调用父组件传递过来的方法
      //this.addTodo(todoObj)
      // or 触发自定义事件
      this.$emit('addEvent', todoObj)
      //清空对话框
      this.userTodo = ''
    }
  },
  // 采用props接收父组件传递过来的方法
  props: ['addTodo'],
  data() {
    return {
      userTodo: ''
    }
  },
}
</script>

<style scoped>
  .todo-header input{
    width: 560px;
    height: 28px;
    font-size: 14px;
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 4px 7px;
  }
  .todo-header input:focus{
    outline: none;
    border-color: rgba(82, 168, 236, 0.8);
    box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
  }
</style>
