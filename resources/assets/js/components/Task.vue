<template>
    <div class="container">

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-default">
                    <div class="panel-heading">
                        My Tasks &nbsp
                        <button @click="initAddTask()" type="button" data-toggle="modal" class="btn btn-primary">
                            Add New
                        </button>
                    </div>

                    <div class="panel-body">
                        <table class="table table-bordered table-striped table-responsive" v-if="tasks.length > 0">
                            <tbody>
                                <tr>
                                    <th>
                                        No.
                                    </th>
                                    <th>
                                        Name
                                    </th>
                                    <th>
                                        Description
                                    </th>
                                    <th>
                                        Action
                                    </th>
                                </tr>
                                <tr v-for="(task, index) in tasks" :key="task.id">
                                    <td>{{ index + 1 }}</td>
                                    <td>
                                        {{ task.name }}
                                    </td>
                                    <td>
                                        {{ task.description }}
                                    </td>
                                    <td>
                                        <button class="btn btn-warning btn-xs">Edit</button>
                                        <button class="btn btn-danger btn-xs">Delete</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" tabindex="-1" role="dialog" id="add_task_model">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span
                                aria-hidden="true">&times;</span></button>
                        <h4 class="modal-title">Add New Task</h4>
                    </div>
                    <div class="modal-body">
 
                        <div class="alert alert-danger" v-if="errors.length > 0">
                            <ul>
                                <li v-for="error in errors" :key="error">{{ error }}</li>
                            </ul>
                        </div>
 
                        <div class="form-group">
                            <label for="name">Name:</label>
                            <input type="text" name="name" id="name" placeholder="Task Name" class="form-control"
                                   v-model="task.name">
                        </div>
                        
                        <div class="form-group">
                            <label for="description">Description:</label>
                            <textarea name="description" id="description" cols="30" rows="5" class="form-control"
                                      placeholder="Task Description" v-model="task.description"></textarea>
                        </div>

                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
                        <button type="button" @click="createTask" class="btn btn-primary">Submit</button>
                    </div>
                </div><!-- /.modal-content -->
            </div><!-- /.modal-dialog -->
        </div><!-- /.modal -->


    </div>

</template>

<script>
    export default {
        data() {
            return {
                task : {
                    name : '',
                    description : ''
                },
                errors : [],
                tasks: []
            }
        },
        mounted()
        {
            this.readTasks();
        },
        methods : 
            {
            initAddTask() {
                this.errors = [];
                $("#add_task_model").modal("show");
            },
            createTask()
            {
                axios.post("/task", {
                    name: this.task.name,
                    description: this.task.description,
                })
                    .then(response => {
 
                        this.reset();

                        this.tasks.push(response.data.task);
 
                        $("#add_task_model").modal("hide");
 
                    })
                    .catch(function (error) {
                        if (error.response) {
                            // The request was made and the server responded with a status code
                            // that falls out of the range of 2xx
                            console.log(error.response.data);
                            console.log(error.response.status);
                            console.log(error.response.headers);
                        } else if (error.request) {
                            // The request was made but no response was received
                            // `error.request` is an instance of XMLHttpRequest in the browser and an instance of
                            // http.ClientRequest in node.js
                            console.log(error.request);
                        } else {
                            // Something happened in setting up the request that triggered an Error
                            console.log('Error', error.message);
                        }
                        console.log(error.config);
                    });
            },
            readTasks()
            {
                axios.get("/task")
                    .then(response => {
 
                        this.tasks = response.data.tasks;
 
                    });
            },
            reset()
            {
                this.task.name = '';
                this.task.description = '';
            },
 
        }
    }
</script>