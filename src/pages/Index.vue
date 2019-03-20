<template>
  <q-page padding>
    <div class="row">

      <q-input v-model="newTask" placeholder="Add new task" class="col" @keyup.enter="addTask"/>
      <q-input v-model="newPriority" placeholder="Add priority" class="priority" @keyup.enter="addTask"/>
      <q-btn
        color="primary"
        @click.native="addTask"
        size="5m"
        label="Add"/>
    </div>
  <div class="q-pa-md" style="max-width: 350px">
    Tasks
        <q-list bordered separator>
      
        <q-item v-for="(task, index) in tasks" :key=index>
        <q-item-section>{{task.title}}</q-item-section>
        <q-item-section avatar>
        <q-icon color="green" name="check" @click.native="moveToDone(index)"/>
        </q-item-section>
      </q-item>
    </q-list>
  </div>
  <div class="q-pa-md" style="max-width: 350px">
          Done

    <q-list bordered separator>
      <q-item v-for="(task, index) in done" :key=index>
        <q-item-section avatar>
          <q-icon color="green" name="arrow_back" @click.native="undoneTask(index)"/>
        </q-item-section>
        <q-item-section>{{task.title}}</q-item-section>
        <q-item-section avatar>
          <q-icon color="red" name="close" @click.native="deleteTask(index)"/>
        </q-item-section>
      </q-item>
    </q-list>
  </div>
  
  </q-page>
</template>

<style>
.priority {
  padding: 10px
}
</style>

<script>
export default {
  data() { 
    return{
      tasks: [],
      done: [],
      newTask:"",
      newPriority: "" 

    }
  },
  methods:{
    addTask(){
      if (this.newTask=="") return
      this.tasks.push({title:this.newTask, priority: this.newPriority!="" && parseInt(this.newPriority)>0? this.newPriority:999})
      this.newTask = ""
      this.newPriority = ""
      this.tasks = this.tasks.slice().sort(function(a, b) {
        return a.priority - b.priority;
      });
      },
    moveToDone(index){
      this.done.push(this.tasks[index])
      this.tasks.splice(index, 1)
    },
    undoneTask(index){
      this.$q.dialog({
        title: 'Confirm',
        message: 'Move task back??',
        ok: {
          push: true
        },
        cancel: {
          color: 'negative'
        },
        persistent: true
      }).onOk(() => {
        this.tasks.push(this.done[index])
        this.done.splice(index,1)
        this.$q.notify("Moved task back!")
      }).onCancel(() =>{
        this.$q.notify("Canceled")
      })
    },
    deleteTask(index){
      this.$q.dialog({
        title: 'Confirm',
        message: 'Really delete?',
        ok: {
          push: true
        },
        cancel: {
          color: 'negative'
        },
        persistent: true
      }).onOk(() => {
        this.done.splice(index,1)
        this.$q.notify("Deleted!")
      }).onCancel(() =>{
        this.$q.notify("Canceled")
      })
    }
  }
}
</script>
