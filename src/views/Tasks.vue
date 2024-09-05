<template>
  <div>
    <h1>taskリスト</h1>
    <el-row style="margin: 10px 0">
      <el-col :span="10">
        <el-input id="title" name="title" placeholder="title" v-model="title"></el-input>
      </el-col>
      <el-col :span="24">
        <el-input id="description" name="description" placeholder="description" v-model="description"></el-input>
      </el-col>
    </el-row>
    <el-row style="margin: 10px 0">
      <el-button type="primary" @click="onSubmit">作成</el-button>
    </el-row>
    <el-row :gutter="12">
      <el-col :span="24"  v-for="task in tasks" :key="task.id">
        <el-card class="box-card" shadow="hover" style="margin: 5px 0">
          <div class="clearfix">
            <el-button style="float: right; margin-top: -5px" @click="deleteTask(task.id)" type="success" icon="el-icon-check" circle></el-button>
            <div>{{task.title}}</div>
            <div>{{task.description}}</div>
          </div>
        </el-card>
      </el-col>
    </el-row>
   </div>
</template>

<script>
import axios from 'axios'

const HTTP = axios.create({
  headers: {
    'Accept': 'application/json',
    'Content-Type':'application/json',
  },
})
const URL_DATA = 'http://localhost:3000/api/v1/tasks/'

export default {
  name: 'Tasks',
  data () {
    return {
      title:"",
      description:"",
      tasks: []
    }
  },

  created() {
    // コンポーネントがマウントされたときにタスクを取得する
    this.getTasks();
  },

  methods: {
    getTasks() {
      HTTP.get(URL_DATA)
        .then((res) => {
          if (res.data.status === 'success') {
            this.tasks = res.data.date || []; // 'date' フィールドにタスクの配列がある
          } else {
            throw new Error('ステータスエラー');
          }
        })
        .catch((error) => {
          console.error('タスク取得エラー:', error);
        });
    },
    onSubmit(){
      HTTP.post(URL_DATA, {
        title: this.title,
        description: this.description
      })
        .then((res) => {
          if (res.status === 200) {
            this.title = "";
            this.description = "";
            // タスクを登録した後にタスク一覧を更新
            this.getTasks(); // 修正: 正しいメソッド名に修正
          } else {
            throw new Error('レスポンスエラー')
          }
        })
        .catch((error) => {
          console.error('タスク登録エラー:', error);
        });
      },
    deleteTask(index){
      HTTP.delete(URL_DATA + index)
      .then((res) => {
        if (res.status === 200) {
          //削除完了
        } else {
          throw new Error('レスポンスエラー')
        }        
        this.getTasks();
      })
    } 
  }
}
</script>