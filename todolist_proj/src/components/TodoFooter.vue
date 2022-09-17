<template>
  <div class="todo-footer" v-show="todoTotal">
    <label>
      <input type="checkbox" v-model="isAll"/>
    </label>
    <span>
      <span>已完成{{doneTotal}}</span> / 全部{{todos.length}}
    </span>
    <button class="btn btn-danger" @click="clearDone">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: 'TodoFooter',
  props: ['todos', 'setAll','removeDoneTodo'],
  computed: {
    todoTotal(){
      return this.todos.length
    },
    doneTotal(){
      return this.todos.reduce((pre, current)=> pre + (current.done?1:0), 0)
    },
    isAll: {
      get(){
        return this.todoTotal === this.doneTotal && this.todoTotal>0
      },
      set(value){
        //将isAll的值传给App，对todos整个List进行修改
        this.setAll(value)
      }
    }
  },
  methods:{
    clearDone(){
      this.removeDoneTodo()
    }
  }
}
</script>

<style scoped>
  .todo-footer{
    height: 40px;
    line-height: 40px;
    padding-left: 6px;
    margin-top: 5px;
  }
  .todo-footer label{
    display: inline-block;
    margin-right: 20px;
    cursor: pointer;
  }
  .todo-footer label input{
    position: relative;
    top: -1px;
    vertical-align: middle;
    margin-left: 5px;
  }
  /*App中对所有button的样式已经有了定义，
    此处针对Footer组件中的按钮单独对其在组件中的位置作定义*/
  .todo-footer button{
    float: right;
    margin-top: 5px;
  }
</style>
