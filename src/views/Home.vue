<template>
<AddTask v-if="showAddTask" @add-task="addTask" />
<div class="tasksDiv">
<Tasks @delete-task='deleteTask' @toggle-reminder='toggleReminder' :tasks='tasks' />
</div>
</template>

<script>
import Tasks from '../components/Tasks.vue'
import AddTask from '../components/AddTasks.vue'
export default {
    name:'Home',
    props:{
        showAddTask:Boolean
    },
    components:{
        Tasks,AddTask,
    },
    data(){
        return {
           tasks:[],
        }
    },
    methods:{
    async addTask(task){
     const res = await fetch('api/tasks',{
       method :'POST',
       headers:{
         'Content-type':'application/json',
       },
        body:JSON.stringify(task)
     })
     const data = await res.json()  
     this.tasks.push(data)
    },
    async deleteTask(id){
      if(confirm('Are you sure you want to delete it?')){
        const res = await fetch(`api/tasks/${id}`,{
       method :'DELETE',
     })
        res.status === 200 ? 
                     (this.tasks = this.tasks.filter(task=>task.id !==id)) 
                    : alert('Error deleting the task')        
      }
    },
    async toggleReminder(id){
      const tasksToToggle = await this.fetchTask(id);
      const updateTask = {...tasksToToggle,reminder:!tasksToToggle.reminder}
      const res = await fetch(`api/tasks/${id}`,{
        method:'PUT',
        headers:{
          'Content-type':'application/json'
        },
        body:JSON.stringify(updateTask)
      })
      const data = await res.json()
      this.tasks = this.tasks.map((task)=>
        task.id === id ? {...task ,reminder:!data.reminder} : task
      )
    },
    async fetchTasks(){
     const res = await fetch('api/tasks');
     const data = await res.json();
     return data;
    },
    async fetchTask(id){
     const res = await fetch(`api/tasks/${id}`);
     const data = await res.json();
     return data;
    }
  },
  async created(){
      this.tasks= await this.fetchTasks();
    },
}
</script>
<style scoped>
  .tasksDiv{
      max-height: 325px;
      overflow: auto;
  }
</style>