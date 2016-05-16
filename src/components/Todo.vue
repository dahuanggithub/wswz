<template>
  <div class="todo">
    
    <div class="todo-add" v-bind:class="{ 'date-btn-active': date }">
      <span v-show="!date">Toto Lists</span>
      <span class="date-btn" v-for="item in date_arr" @click="date_select($index)" v-bind:class="{ 'date-btn-end': !date,'date-btn-top':item.z} ">{{ item.text }}</span>
      <span v-on:click="toggle_add()" class="add-btn" v-bind:class="{ 'add-btn-active': add }"></span>
    </div>
    <div class="add-new" v-bind:class="{ 'add-new-show': add }">

    <textarea name="" id="" cols="30" rows="7" v-show="add" placeholder="What would you record ?" v-model="add_todo"></textarea>

    <div class="todo-time-set" v-show="add">
      <i class="iconfont_todo" v-bind:class="{ 'todo-alert-icon-true': range>=0 }" @click="reset_time">&#xe600;</i>
      <span>{{ range_time }}</span>
      <input type="range" name="points" min="-1" max="102" v-model = "range"/>

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
        <div class="todo-content" @click="modify_todo($index,todo._id)">
          {{ todo.content }}
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import localdb from './localdb'
const Notes = new localdb('notes', 'Array', true)

  export default {
    data: function() {
      return{
        add: false,
        modify: false,
        date: false,
        date_selected:'',
        modify_index: 0,
        modify_id: 0,
        range: -1,
        add_todo:'',
        todos: [],
      }
    },
    computed:{
       range_time: function(range){
          if(this.range<0){
            return '不提醒'
          }else{
            var h=parseInt(this.range/6)+5
            var m=this.range%6*10
            m=m?m:'00'
            var t=h+':'+m
            return t
          }
        },
        date_arr: function(){
          let date_array =[]
          let mydate = new Date()
          let date_utc = mydate.getTime()
          for (var i = 4; i >= 0; i--) {
            let date_temp = new Date()
            date_temp.setTime(date_utc + 86400000*i)
            let date = date_temp.getFullYear()*10000+date_temp.getMonth()*100+date_temp.getDate()
            date_array[i] = {date: date,z: false,text: date_temp.getDate()}
          }
          date_array[0].text = '今天'
          date_array[0].z = true
          date_array[1].text = '明天'
          return date_array
        },
        date_today: function(){
          let mydate = new Date()
          return mydate.getFullYear()*10000+mydate.getMonth()*100+mydate.getDate()
        },
        todos_local: function(){
          let notes = Notes
          let respose = notes.get()
          return respose
        },
        todos_one: function(){
          let date = this.date_selected?this.date_selected:this.date_today
        }

    },
    ready: function(){
      let notes = Notes
      this.todos = notes.get()
    },
    methods: {
      toggle_alert: function(index){
        this.todos[index].alert = this.todos[index].alert?false:true
      },
      toggle_add: function(){
        this.add_todo = ''
        this.range = -1
        this.add = this.add?false:true
      },
      reset_time: function(){
        this.range = -1
      },
      date_select: function(index){
        if(this.date){
          this.date_arr[index].z = true
          this.date_selected = this.date_arr[index].date
        }else{
          for (var i = 4; i >= 0; i--) {
            this.date_arr[i].z =false
          }
        }
        this.date = this.date?false:true
      },
      submit_todo: function(){
        let notes = Notes
        let text = this.add_todo.trim()
        let time = this.range_time.trim()
        let range = this.range
        let date = this.date_selected?this.date_selected:this.date_today

        if(this.modify == true){
          let index = this.modify_index
          let id = this.modify_id
          let query = {'_id': id}
          let opts ={limit: 1,sort: 1, sortBy: '_id',skip: 0}
          console.log(Notes.findOne(query, opts).content)
          if(text){
            this.todos[index].content = text
            this.todos[index].range = range
            let notes = []
            if(time == '不提醒'){
              this.todos[index].alert = false
              notes.push(Notes.find(query,opts))
              notes.forEach(note=>{
                note.content = text
                note.range = range
                note.alert = false
                note.date = date
                Notes.save(note)
              })
            }else{
              this.todos[index].alert = true
              this.todos[index].time = time
              notes.push(Notes.find(query,opts))
              notes.forEach(note=>{
                note.content = text
                note.range = range
                note.alert = true
                note.time = time
                note.date = date
                Notes.save(note)
              })
              this.range = -1
            }
          }else{
            Notes.remove('_id',id)
            this.todos.splice(index,1)
          }
          this.add = false
          this.modify = false
          this.add_todo = ''
          return
        }
        if(text){
          if(time=='不提醒'){
            notes.add({content: text,alert: false,time:'',range: -1,date: date})
            this.todos = notes.get()
          }else{
            notes.add({content: text,alert: true,time: time,range: range,date: date})
            this.todos = notes.get()
            this.range = -1
          }
          this.add = false
          this.add_todo = ''
        }
      },
      modify_todo: function(index,id){
        let notes = Notes
        let query = {'_id': id}
        let opts ={limit: 1,sort: 1, sortBy: '_id',skip: 0}
        let todo_select = notes.findOne(query, opts)
        this.add = true
        this.modify = true
        this.modify_index = index
        this.modify_id = id
        this.add_todo = todo_select.content
        if(todo_select.range&&todo_select.alert==true){
          this.range = todo_select.range
        }else{
          this.range = -1
        }

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
  margin-top: 260px;
  width: 100%;
  
}
.todo-add{
  position: absolute;
  width: 100%;
  height: 50px;
  box-shadow: 0 1px 2px rgba(150,150,150,.1);
}
.todo-add span:nth-child(1){
 color: #999;
 font-size: 18px;
 line-height: 50px;
 position: relative;
 top: -4px;
 left: 60px;
 float: left;
}
.todo-add span:nth-child(2){
  background-color: #0e9cfb;
  z-index: 1;
  -webkit-transition: left 0.05s ease;
  -o-transition: left 0.05s ease;
  transition: left 0.05s ease;
}
.todo-add span:nth-child(3){
  background-color: #36adfc;
  z-index: 1;
  -webkit-transition: left 0.13s ease;
  -o-transition: left 0.13s ease;
  transition: left 0.13s ease;
}
.todo-add span:nth-child(4){
  background-color: #5ebdfd;
  z-index: 1;
  -webkit-transition: left 0.16s ease;
  -o-transition: left 0.16s ease;
  transition: left 0.16s ease;
}
.todo-add span:nth-child(5){
  background-color: #96cefd;
  z-index: 1;
  -webkit-transition: left 0.19s ease;
  -o-transition: left 0.19s ease;
  transition: left 0.19s ease;
}
.todo-add span:nth-child(6){
  background-color: #afdefd;
  z-index: 1;
  -webkit-transition: left 0.22s ease;
  -o-transition: left 0.22s ease;
  transition: left 0.22s ease;
}
.date-btn-top{
  z-index: 3!important;
}
.date-btn{
  position: absolute;
  left: 10px;
  display: block;
  width: 40px;
  height: 40px;
  border: 0;
  background-color: #3DAEFE;
  z-index: 10;
  font-size: 13px;
  box-shadow: 0 0 2px rgba(0,0,0,.1) inset;
  text-shadow: 0 1px 2px rgba(0,0,0,.2);
  color: #fff;
  line-height: 40px;
}
.date-btn-end{
  -webkit-animation: datebtnend 0.2s cubic-bezier(0, 0.42,1, 0.58) 0.2s;
  -o-animation: datebtnend 0.2s cubic-bezier(0, 0.42,1, 0.58) 0.2s;
  animation: datebtnend 0.2s cubic-bezier(0, 0.42,1, 0.58) 0.2s;
}
@-webkit-keyframes datebtnend {
  0%     { left: 10px; }
  50%   { left: 5px; }
  100% { left: 10px; }
}
@-o-keyframes datebtnend {
  0%     { left: 10px; }
  50%   { left: 5px; }
  100% { left: 10px; }
}
@-moz-keyframes datebtnend {
  0%     { left: 10px; }
  50%   { left: 5px; }
  100% { left: 10px; }
}
@keyframes datebtnend {
  0%     { left: 10px; }
  50%   { left: 5px; }
  100% { left: 10px; }
}

.date-btn-active span:nth-child(3){
  left: 60px;
}
.date-btn-active span:nth-child(4){
  left: 110px;
}
.date-btn-active span:nth-child(5){
  left: 160px;
}
.date-btn-active span:nth-child(6){
  left: 210px;
}
.add-btn{
  position: absolute;
  right: 10px;
  display: block;
  width: 40px;
  height: 40px;
  border: 0;
  border-radius: 100%;
  background-color: #00D92C;
  box-shadow: 0 4px 5px rgba(0,197,40,.25);
  -webkit-transition: all 0.25s ease;
  -o-transition: all 0.25s ease;
  transition: all 0.25s ease;
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
  background-color: #fdfdfd;
}
.todo-time div{
  width: 100%;
  height: 50%;
}
.todo-time div:nth-child(2){
  background: #f5f5f5;
  color: #aaa;
  font-weight: 100;
  font-size: 13px;
  line-height: 24px;
}
.todo-content{
  padding: 14px 0 14px 20px;
  color: #aaa;
  width: 85%;
  height: 50px;
  overflow: hidden;
  float: left;
  text-align: left;
  font-size: 16px;
  border-bottom: 1px solid #f5f5f5;
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