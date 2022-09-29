<template>
  <li>
    <label>
      <input type="checkbox" :checked="todo.done" @change="transferChange(todo.id)"/>
      <span v-show="!todo.isEdit">{{todo.title}}</span>
      <input v-show="todo.isEdit" 
        :value="todo.title"
        @blur="lostFocus(todo, $event)"
        ref="inputBlock"
        />
    </label>
    <button class="btn btn-danger" @click="deleteBtnClicked(todo.id)">删除</button>
    <button class="btn btn-edit" @click="editBtnClicked(todo)">编辑</button>
  </li>
</template>

<script>
  import { ReturnStatement } from 'babel-generator/lib/generators/statements'
import pubsub from 'pubsub-js'
  export default {
    name: 'TodoItem',
    // 接收父组件传递过来的方法
    //props: ['todo', 'changeState', 'removeTodo'],
    props: ['todo'],
    methods: {
      //将状态发生变化的todo对象的id传递给父元素
      transferChange(id){
        //调用父组件传递过来的方法
        //this.changeState(id)
        // or 使用全局事件总线，触发事件。ps:就算事件已经被解绑了还是可以触发事件，只是没有回调函数来响应这个事件了
        this.$bus.$emit('itemChange', id)
      },
      //将按下删除按钮的todo对象的id传递
      deleteBtnClicked(id){
        if(confirm('确定要删除吗？'))
          //1.调用父组件传递过来的方法
          //this.removeTodo(id)
          //2.使用全局事件总线，触发事件
          //this.$bus.$emit('itemRemove', id)
          //3.使用消息订阅与发布
          pubsub.publish('removeMsg', id)
      },
      //按下编辑按钮后对文本框进行编辑操作
      editBtnClicked(todo){
        //若todo身上没有isEdit属性(即第一次进行编辑时)，对其进行添加
        if(!todo.hasOwnProperty('isEdit')){
          //使用$set方法往vc实例上添加响应式属性
          this.$set(todo, 'isEdit', true)
        }
        else{
          //否则直接修改它的值
          todo.isEdit = true
        }
        //在数据变更引起DOM重新解析后，调用下述回调
        this.$nextTick(()=>{
          this.$refs.inputBlock.focus()
        })
      },
      lostFocus(todo, e){
        //当input框失去焦点时修改isEdit的值
        todo.isEdit = false
        //若input框的内容为空时直接返回并弹窗提示
        if (!e.target.value)
        {
          alert('输入不能为空!')
          return
        }
        //将input框的内容同步至todo数据中
        this.$bus.$emit('itemUpdate', todo.id, e.target.value)   //全局事件总线
      } 
    }
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
