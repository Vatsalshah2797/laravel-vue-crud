<template>
    <div class="container">
        <div class="row justify-content-center mt-5">
            <div class="col-md-6">
                <div class="card">
                    <div class="card-header">Todo Web Application</div>

                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" placeholder="Todo..." class="form-control" aria-label="todo" aria-describedby="todo" v-model="todo_input" >
                            <div class="input-group-append pl-2">
                                <button type="button" class="btn btn-info" @click="saveTodo()" v-if="!edit_todo_id">Add</button>

                                <button type="button" class="btn btn-warning" @click="updateTodo()" v-else="edit_todo_id">Update</button>

                                <button type="button" class="btn btn-danger float-right" @click="resetTodo()" >Reset</button>
                            </div>
                        </div>
                        <table class="table table-bordered mt-4">
                            <thead>
                                <th>Sr No</th>
                                <th>Name</th>
                                <th>Action</th>
                            </thead>
                            <tbody>
                                <tr v-for="(todo,index) in todos" :key="index">
                                    <td> {{ ++index }} </td>
                                    <td> {{ todo.name }} </td>
                                    <td> 
                                        <button type="button" class="btn btn-warning ml-10" @click="editTodo(todo.id, --index)">Edit </button>

                                        <button type="button" class="btn btn-danger mr-4" @click="deleteTodo(todo.id, --index)"> Delete </button>
                                    </td>
                                </tr>
                                
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        //Declare Variable
        data() {
            return {
                todos:[],
                api: "http://localhost:8000/api/todos",
                todo_input: '',
                edit_todo_id: '',
                edit_todo_index: '',
            }
        },
        //Set Value for the todo list :: Read Fetch All Todo List data
        mounted() {
            console.log('Component mounted.')
            this.axios.get(this.api).then((response) => {
                console.log(response.data);
                this.todos = response.data;
            });
        },
        //On click perform  Create store, Edit, Update, Delete o 
        methods:{
            saveTodo() {
                console.log(this.todo_input);
                if(this.todo_input.length > 0) {
                    let data = {'name': this.todo_input};
                    this.axios.post(this.api, data).then((response) => {
                        console.log(response);
                        this.todos.push(response.data);
                        this.todo_input = '';
                    });
                } else {
                    alert('Please enter todo name');
                }
            }, 
            deleteTodo(id, index) {
                //console.log(id);
                //console.log(index);
                //return false;
                if(id > 0) {
                    this.axios.delete(this.api+'/'+id).then((response) => {
                        console.log(response);
                        this.todos.splice(index, 1);
                    });
                }
            },
            editTodo(id, index) {
                if(this.todos[index].id) {
                    this.todo_input = this.todos[index].name;
                    this.edit_todo_id = id;
                    this.edit_todo_index = index;
                }
            },
            updateTodo() {
                if(this.edit_todo_id > 0) {
                    let data = {'name': this.todo_input};
                    this.axios.put(this.api+'/'+this.edit_todo_id, data).then((response) => {
                        console.log(response);
                        this.todos[this.edit_todo_index].name = response.data.name;
                        this.resetTodo();
                    });
                }
            },
            resetTodo() {
                this.todo_input = '';
                this.edit_todo_id = '';
                this.edit_todo_index = '';
            },
        },
        
    }
</script>
