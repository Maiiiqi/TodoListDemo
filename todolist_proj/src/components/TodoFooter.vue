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
  //接收父组件传递过来的数据以及方法
  //props: ['todos', 'setAll','removeDoneTodo'],
  //只接受数据
  props: ['todos'],
  computed: {
    todoTotal(){
      return this.todos.length
    },
    doneTotal(){
      //reduce方法对数组进行筛选和统计
      return this.todos.reduce((pre, current)=> pre + (current.done?1:0), 0)
    },
    isAll: {
      get(){
        return this.todoTotal === this.doneTotal && this.todoTotal>0
      },
      set(value){
        //将isAll的值传给App，对todos整个List进行修改
        //调用父组件传递过来的方法
        //this.setAll(value)
        //or 触发自定义事件
        this.$emit('setAll', value)
      }
    }
  },
  methods:{
    clearDone(){
      //调用父组件传递过来的方法
      //this.removeDoneTodo()
      //or 触发自定义事件
      this.$emit('removeDoneTodo')
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
