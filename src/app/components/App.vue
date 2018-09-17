<template>
    <div>
        <nav class="navbar navbar-light bg-light">
            <a href="/" class="navbar-brand">MENV Stack</a>
        </nav>

        <div class="container">
            <div class="row">
                <div class="col-md-5">
                    <div class="card-body">
                        <form @submit.prevent="sendTask">
                            <div class="form-group">
                                <input type="text"
                                    v-model="task.title"
                                    placeholder="Insert a Task"
                                    class="form-control">
                            </div>
                            <div class="form-group">
                                <textarea cols="30" rows="10"
                                    v-model="task.description"
                                    class="form-control"
                                    placeholder="Insert a Description"
                                    ></textarea>
                            </div>
                            <template v-if="isEdit === false">
                                <button class='btn btn-primary btn-block'>Send</button>
                            </template>
                            <template v-else>
                                <button class='btn btn-primary btn-block'>Update</button>
                            </template>
                        </form>
                    </div>
                </div>
                <div class="col-md-7">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>Task</th>
                                <th>Description</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="task of tasks">
                                <td>{{task.title}}</td>
                                <td>{{task.description}}</td>
                                <td>
                                    <button
                                        @click="deleteTask(task._id)"
                                        class="btn btn-danger"
                                        >Delete</button>
                                    <button
                                        @click="editTask(task._id)"
                                        class="btn btn-secundary"
                                        >
                                       Edit 
                                    </button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
// class Task {
//     constructor(title, description){
//         this.title = title,
//         this.description = description
//     }
// }

export default {
    data() {
        return {
            task: {
                title: '',
                description: ''
            },
            tasks: [],
            isEdit: false,
            taskToEdit: ''
        }
    },
    created(){
        this.getTask()
    },
    methods: {
        getTask(){
            fetch('api/tasks')
                .then(res => res.json())
                .then(data => {
                    this.tasks = data
                })
        },
        sendTask(){
            if(this.isEdit === false){
                fetch('api/tasks', {
                    method: 'POST',
                    body: JSON.stringify(this.task),
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.getTask()
                })
            } else {
                fetch('api/tasks/' + this.taskToEdit, {
                    method: 'PUT',
                    body: JSON.stringify(this.task),
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.getTask()
                    this.isEdit = false
                })
            }


            this.task.title = ''
            this.task.description = ''
        },
        deleteTask(id){
            fetch('api/tasks/' + id,{
                method: 'DELETE',
                headers: {
                    'Accept': 'application/json',
                    'Content-type': 'application/json'
                }
            })
            .then(res => res.json())
            .then(data => this.getTask())

        },
        editTask(id){
            fetch('api/tasks/' + id)
                .then(res => res.json())
                .then(data => {
                    this.task = {
                        title: data.title,
                        description: data.description
                    }
                    this.isEdit = true
                    this.taskToEdit = id
                })
        }
    }
}
</script>

