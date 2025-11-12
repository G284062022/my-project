<script setup>
import { ref } from 'vue'

const str = ref('')
const serverReply = ref('')         // 用来显示后端返回值（测试用）
const todos = ref([
  { id: 1, text: '吃饭', done: true },
  { id: 2, text: '吃饭', done: false },
  { id: 3, text: '吃饭', done: false }
])

function nextId() {
  return Date.now() + Math.floor(Math.random() * 1000)
}

async function add() {
  // 1) 前端本地逻辑：把输入加入 todos（你可以按需修改）
  const text = (str.value || '').trim()
  if (!text) {
    alert('请输入内容')
    return
  }
  todos.value.unshift({ id: nextId(), text, done: false })
  str.value = ''

  // 2) 测试后端连通：请求后端 hello 接口并显示返回（可选）
  try {
    // 如果你在 vite.config.js 配置了 proxy '/api' -> 'http://localhost:8080'
    // 可以把下面改为 fetch('/api/hello')
    const res = await fetch('http://localhost:8080/hello')
    if (res.ok) {
      const txt = await res.text()
      serverReply.value = txt
    } else {
      serverReply.value = `后端返回错误: ${res.status}`
    }
  } catch (err) {
    console.error('请求后端失败', err)
    serverReply.value = '请求后端失败（看控制台）'
  }
}

// 删除 item
function remove(id) {
  todos.value = todos.value.filter(t => t.id !== id)
}

// 切换完成状态
function toggleDone(item) {
  item.done = !item.done
}
</script>

<template>
  <div class="todo-app">
    <div class="title">ランのAPP</div>

    <div class="todo-from">
      <input v-model="str" class="todo-input" type="text" placeholder="ID">
      <div @click="add" class="todo-button">add Todo</div>
    </div>

    <!-- 显示后端返回（测试用） -->
    <div style="margin-left:30px; margin-top:10px; color: #1a73e8;">
      後端返回：<strong>{{ serverReply }}</strong>
    </div>

    <!-- 动态渲染 todos -->
    <div v-for="item in todos" :key="item.id" :class="['item1', { completed: item.done }]">
      <div class="hezi">
        <input type="checkbox" :checked="item.done" @change="toggleDone(item)" />
        <div class="name" style="margin-left:10px">{{ item.text }}</div>
      </div>
      <div class="del" @click="remove(item.id)">del</div>
    </div>

    <!-- 空列表提示 -->
    <div v-if="todos.length === 0" style="text-align:center; margin-top:30px; color:gray">
      まだTodoがありません
    </div>
  </div>
</template>

<style scoped>
body{
background:linear-gradient(to right,purple,blue) ;
}
.todo-app{
    width: 98%;
    /* 用 auto 高度让内容扩展 */
    min-height: 300px;
    padding: 30px;
    box-sizing: border-box;
    background-color: aliceblue;
    border-radius: 5px;
    margin-top: 40px;
    margin-left: 1%;
}
.title{
    font-size: 30px;
    font-weight: 700px;
    text-align: center;
}
.todo-from{
    display: flex;
    margin-top: 20px;
    margin-left: 30px;
}
.todo-input{
    border: 1px solid darkgrey;
    outline: none;
    width: 60%;
    height: 50px;
    border-radius: 20px 0 0 20px;
    padding-left: 15px;
    margin-bottom: 20px;
}
.todo-button{
    width: 100px;
    height: 54px;
    border-radius: 0 20px 20px 0;
    color: aliceblue;
    background:linear-gradient(to right,purple,blue) ;
    text-align: center;
    line-height: 54px;
    cursor: pointer;
    user-select: none;
}
.item1{
    display: flex;
    align-items: center;
    justify-content: space-between;
    box-sizing: border-box;
    width: 80%;
    height: 50px;
    margin: 8px auto;
    padding: 16px;
    border-radius: 20px 20px 20px 20px;
    box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 20px;
}
.hezi{
    display: flex;
    align-items: center;
}
.del{
    color: red;
    cursor: pointer;
}
.completed{
    text-decoration: line-through;
    opacity: 0.4;
}
</style>
