<template>
  <li>
    <label>
      <input type="checkbox" :checked="todo.done" @change="transferChange(todo.id)"/>
      <span>{{todo.title}}</span>
    </label>
    <button class="btn btn-danger" @click="deleteBtnClicked(todo.id)">删除</button>
  </li>
</template>

<script>
export default {
  name: 'TodoItem',
  props: ['todo', 'changeState', 'removeTodo'],
  methods: {
    //将状态发生变化的todo对象的id传递给父元素
    transferChange(id){
      this.changeState(id)
    },
    //将按下删除按钮的todo对象的id传递
    deleteBtnClicked(id){
      if(confirm('确定要删除吗？'))
        this.removeTodo(id)
    }
  },
}
</script>

<style scoped>
  li{
    list-style: none;
    height: 36px;
    line-height: 36px;
    padding: 0 5px;
    border-bottom: 1px solid #ddd;
  }
  li label{
    float: left;
    cursor: pointer;
  }
  li label li input{
    vertical-align: middle;
    margin-right: 6px;
    position: relative;
    top: -1px;
  }
  li button{
    float: right;
    display: none;
    margin-top: 3px;
  }
  li:before{
    content: initial;
  }
  li:last-child{
    border-bottom: none;
  }
  li:hover{
    background-color: #ddd;
  }
  li:hover button{
    display: block;
  }
</style>
