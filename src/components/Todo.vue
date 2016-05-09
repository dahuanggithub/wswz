<template>
  <div class="todo">
    
    <div class="todo-add">
      <span>Todo List</span>
      <span v-on:click="toggle_add()" class="add-btn" v-bind:class="{ 'add-btn-active': add }"></span>
    </div>
    <div class="add-new" v-bind:class="{ 'add-new-show': add }">

    <textarea name="" id="" cols="30" rows="7" v-show="add" placeholder="What would you record ?" v-model="add_todo"></textarea>

    <div class="todo-time-set" v-show="add">
      <i class="iconfont_todo" v-bind:class="{ 'todo-alert-icon-true': range>=0 }" @click="reset_time">&#xe600;</i>
      <span>{{ range_time }}</span>
      <input type="range" name="points" min="-1" max="72" v-model = "range"/>

      <span class="ok-btn" @click="submit_todo"></span>

    </div>
    </div>
    <div class="todo-lists">
      <div class="todo-list" v-for = "todo in todos">
        <div class="todo-time">
          <div v-on:click="toggle_alert($index)">
            <i class="iconfont_todo todo-alert-icon" v-bind:class="{ 'todo-alert-icon-true': todo.alert }">&#xe600;</i>
          </div>
          <div>{{ todo.time }}</div>
        </div>
        <div class="todo-content" @click="modify_todo($index)">
          {{ todo.content }}
        </div>
      </div>
    </div>
  </div>
</template>


<script>
  export default {
    data: function() {
      return{
        add: false,
        range: -1,

        add_todo:'',
        todos: []


      }
    },
    computed:{
       range_time: function(range){
          if(this.range<0){
            return '不提醒'
          }else{
            var h=parseInt(this.range/6)+7
            var m=this.range%6*10
            m=m?m:'00'
            var t=h+':'+m
            return t
          }
        },
    },
    methods: {
      toggle_alert: function(index){
        this.todos[index].alert = this.todos[index].alert?false:true
      },
      toggle_add: function(){
        this.add = this.add?false:true
      },
      reset_time: function(){
        this.range = -1

      },
      submit_todo: function(){
        var text = this.add_todo.trim()
        var time = this.range_time.trim()
        if(text){
          if(time=='不提醒'){
          this.todos.push({content: text,alert: false,})
          }else{
            this.todos.push({content: text,alert: true,time: time})
            this.range = -1
          }
          this.add = false
          this.add_todo = ''
        }
      },
      modify_todo: function(index){
        this.add = true
        this.add_todo = this.todos[index].content
      }
    }
  }
</script>


<style scoped>
@font-face {
  font-family: 'iconfont_todo';
  src: url('../assets/todo_iconfont/iconfont.eot'); /* IE9*/
  src: url('../assets/todo_iconfont/iconfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('../assets/todo_iconfont/iconfont.woff') format('woff'), /* chrome、firefox */
  url('../assets/todo_iconfont/iconfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
  url('../assets/todo_iconfont/iconfont.svg#iconfont') format('svg'); /* iOS 4.1- */
}
.iconfont_todo{
  font-family: "iconfont_todo" !important;
  font-size: 24px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
}
.todo{
  position: absolute;
  margin-top: 270px;
  width: 100%;
  
}
.todo-add{
  position: absolute;
  width: 100%;
  height: 50px;
  background: rgba(255,255,255,.8);
}
.todo-add span:nth-child(1){
 color: #999;
 font-size: 18px;
 line-height: 50px;
 position: relative;
 left: 15px;
 float: left;
}
.add-btn{
  position: absolute;
  right: 15px;
  display: block;
  width: 40px;
  height: 40px;
  border: 0;
  border-radius: 100%;
  background-color: #00D92C;
  box-shadow: 0 4px 5px rgba(0,197,40,.25);
  -webkit-transition: all 0.25s;
  -o-transition: all 0.25s;
  transition: all 0.25s;
  z-index: 10;
}
.add-btn:after,.add-btn:before{
  position: absolute;
  left: 10px;
  top: 18px;
  content: "";
  width: 20px;
  height: 4px;
  background-color: rgba(255,255,255,.8);
}
.add-btn:after{
  top: 10px;
  left: 18px;
  width: 4px;
  height: 20px;
}
.add-btn-active{
  -webkit-transform: rotate(-135deg);
  -ms-transform: rotate(-135deg);
  -o-transform: rotate(-135deg);
  transform: rotate(-135deg);
  background-color: #F54263;
  box-shadow: -3px -3px 5px rgba(223,19,40,.2);
}

.todo-lists{
  margin-top: 50px;
  height: 270px;
  overflow-y: scroll;
}
.todo-list{
  width: 100%;
  height: 50px;
}
.todo-time{
  float: left;
  width: 15%;
  height: 100%;
  background-color: #f3f3f3;
}
.todo-time div{
  width: 100%;
  height: 50%;
}
.todo-time div:nth-child(2){
  background: #fdfdfd;
  color: #aaa;
  font-size: 16px;
}
.todo-content{
  padding: 14px 0 14px 20px;
  color: #aaa;
  width: 75%;
  overflow: hidden;
  float: left;
  text-align: left;
  font-size: 16px;
  border-top: 1px solid #f3f3f3;
}
.todo-alert-icon{
  font-size: 24px;
  line-height: 22px;
  color: #ccc;
}
.todo-alert-icon-true{
  color: #12C902;
}
.add-new{
  position: absolute;
  margin-top: 50px;
  width: 100%;
  height: 0px;
  background: #fff;
  overflow: hidden;
  -webkit-transition: height 0.3s;
  -o-transition: height 0.3s;
  transition: height 0.3s;
}
.add-new-show{
  height: 250px;
}
textarea{
  width: 90%;
  border: 0;
  font-size: 16px;
  line-height: 20px;
  font-weight: 100;
}
.todo-time-set{
  margin-top: 20px;
  margin-left: 20px;
  text-align: left;
  font-size: 14px;
  line-height: 30px;
  color: #999
}
.todo-time-set span{
  display: inline-block;
  width: 48px;
  height: 30px;
  line-height: 30px;
}
.todo-time-set i{
  display: inline-block;
  height: 30px;
  line-height: 30px;
  font-size: 30px;
}
input[type="range"] {
    -webkit-appearance: none !important;
    position: relative;
    top: -4px;
    margin-top: 0px;
    background-color: #00D92C;
    border-radius: 15px;
    width: 50%;
    height: 2px;
}
input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none !important;
    cursor: default;
    top: 1px;
    height: 30px;
    width: 30px;
    background: #fafafa;
    border-radius: 100%;
    border: transparent;
    box-shadow: 0 1px 2px rgba(100,100,100,.5);
}
.ok-btn{
  position: absolute;
  right: 15px;
  display: block;
  width: 40px!important;
  height: 40px!important;
  border: 0;
  border-radius: 100%;
  background-color: #00D92C;
  box-shadow: 0 4px 5px rgba(0,197,40,.25);
  -webkit-transition: all 0.25s;
  -o-transition: all 0.25s;
  transition: all 0.25s;
}
.ok-btn:before,.ok-btn:after{
  content: "";
  display:block; 
  position:absolute; 
  top:10px; 
  left: 20px; 
  height:20px; 
  width:4px; 
  background-color: rgba(255,255,255,.8);
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  -o-transform: rotate(45deg);
  transform: rotate(45deg);
}
.ok-btn:before{
  top:18px; 
  left:12px;
  height:10px;
  -webkit-transform: rotate(-45deg);
  -ms-transform: rotate(-45deg);
  -o-transform: rotate(-45deg);
  transform: rotate(-45deg);
}
</style>